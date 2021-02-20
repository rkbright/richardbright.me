---
layout:     post
title:      Using GitHub for Jekyll Comments
date:       2021-02-20
summary:    GitHub issues, an alternative to using disqus
permalink: /using-github-for-comments/
toc: true
tags:
  - jekyll
  - github
---

![GitHud Jekyll Picture](https://richardbright.me/images/gj-pic.png){:class="img-responsive"}

Tired of paying for a commenting service? If you're using Jekyll, this article is a quick walk through to show one way you can add comments to your blog using GitHub issues. 

You'll need to implement 4 things:
  
  1) add a file that maps your post permalink to the GitHub issue
  2) add a `comment.html` file to your `_includes` folder that will call the GitHub API and render comments
  3) add a script that will generate and update comments 
  4) add a `comments.css` file to format how comments are rendered


  ### **Mapping your post to a GitHub**

  I have a `_data` folder in my directory tree and is where I placed the mapping file. Please note that the permalink in this file should match the permalink in your post front matter. Also do not forget to update the `issueLink` and `apiLink` variables below to match your GitHub account and repo.
  
  `commentsrefs.yml`   

        /post-1-permalink/: 1
        /post-2-permalink//: 2
        /post-3-permalink//: 3
        /post-13-permalink//: 13 

### **Adding a comments page**

Include this in your `_includes` folder so that you can add it to your `post.html` page. 

`comments.html`

    {% assign issue_number = site.data.commentrefs[post.permalink] %}
    {% raw %}
    <div class="comments plain_links" ng-app="blog-comments" ng-controller="CommentsController">
        <h2>Comments</h2>
        <div ng-if="comments.length > 0 || isLoading">
            <a ng-href="{{issueLink}}"><i class="fa fa-plus"></i> Add a comment</a> with your GitHub account
        </div>
        <div class="loading" ng-if="isLoading">
            <i class="fa fa-spinner fa-2"></i> Loading comments...
        </div>
        <div ng-if="comments.length == 0 && !isLoading" class="no-comments">
            No comments yet on this post. Why don't you <a ng-href="{{issueLink}}">be the first one</a>? By the way, you will need a GitHub account to comment.
        </div>
        <div ng-if="comments.length > 0 && !isLoading" ng-repeat="c in comments" class="comment">
            <div class="comment-header">
                <div class="comment-avatar">
                    <span class="dummy"></span>
                    <img ng-src="{{c.user.avatar_url}}" />
                </div>
                <a class="commenter" href="https://www.github.com/{{c.user.login}}">{{c.user.login}}</a>
                <time datetime="{{c.created_at}}" title="{{dateTitle(c.created_at)}}">on {{c.created_at | date: "MMM dd, yyyy"}}</time>
            </div>
            <div class="comment-body" ng-bind-html="c.body_html | rawHtml">
            </div>
        </div>
    </div>
    {% endraw %}

    <script type="text/javascript">
    (function(ng){
        
        function CommentsController($scope, $http, $filter){
            $scope.isLoading = true;
            $scope.comments = [];
            $scope.issueLink = "https://github.com/USERID/REPO/issues/{{issue_number}}";
            
            var apiLink = "https://api.github.com/repos/USERID/REPO/issues/{{issue_number}}/comments";
            
            $scope.dateTitle = function(date){
                return $filter("date")(date, "MMMM dd, yyyy '('hh:mm a 'GMT'Z')'")
            };
            
            function init(){
                $http
                .get(apiLink, {headers: {Accept: "application/vnd.github.full+json"}})
                .then(function(res){
                    return $filter("orderBy")(res.data, "created_at");
                })
                .then(function(comments){
                    $scope.isLoading = false;
                    $scope.comments = comments;
                });
            };
            
            init();
        };
        
        ng
        .module("blog-comments", [])
        .controller("CommentsController", ["$scope", "$http", "$filter", CommentsController])
        .filter('rawHtml', ['$sce', function($sce){
            return function(val) {
                return $sce.trustAsHtml(val);
            };
        }]);
    })(angular);
    </script>

### **Add script to generate and update comments**

I have a general `_scripts` I use to organize various scripts on the site. Create a `.js` file and add the following code. You will also need to update the following variables with your information: `siteRoot` and `msg`. 


    var GitHubApi = require("github"),
        q = require("q"),
        yaml = require("js-yaml"),
        fs = require('q-io/fs'),
        frontmatter = require("front-matter"),
        util = require("util");

    var github = new GitHubApi({
        version: "3.0.0",
        debug: false,
        protocol: "https",
        host: "api.github.com",
        pathPrefix: "",
        timeout: 5000
    });

    const dataFilePath = __dirname + '/../_data/commentrefs.yml';
    const POSTS_DIR = __dirname + '/../_posts/';
    const siteRoot = "https://DOMAIN";

    function getExistingCommentLinks(){
        return fs.read(dataFilePath, {'charset': 'utf8', 'flags': 'r'})
        .then(function(content){
            return yaml.safeLoad(content);
        });
    };

    function readSingleFileFrontMatter(file){
        return fs.read(POSTS_DIR + file, {'charset': 'utf8', 'flags': 'r'})
        .then(function(content){
            return frontmatter(content);
        })
        .then(function(obj){
            return obj.attributes;
        });
    };

    function isCommentLinked(frontMatterAttr, existingCommentLinks){
        return (existingCommentLinks && existingCommentLinks[frontMatterAttr.permalink]);        
    }

    function getUnlinkedPostFrontMatters(existingCommentLinks){
        return fs.list(POSTS_DIR)
        .then(function(files){
            var fmPromises = files.map(function(file){
                return readSingleFileFrontMatter(file);
            });
            
            return q.all(fmPromises);
        })
        .then(function(attrList){
            return attrList.filter(function(attr){
                return !isCommentLinked(attr, existingCommentLinks);
            });
        });
    };

    function createSingleIssue(fmAttr){
        const bodyTemplate = "Auto-generated issue to track comments for the post [%s](%s%s) in my [blog](%s).";
        
        var body = util.format(bodyTemplate, fmAttr.title, siteRoot, fmAttr.permalink, siteRoot);
        
        var msg = {
            user: "USERID",
            repo: "REPO.",
            title: fmAttr.title,
            body: body,
            labels: []
        };
        
        var deferred = q.defer();
        
        github.issues.create(msg, function(err, res){
            if(err){
                deferred.reject(err);
            }
            else if(res){
                deferred.resolve({link: fmAttr.permalink, number: res.number});
            }
        });
        
        return deferred.promise;
    };

    function saveNewCommentLinks(maps){
        if(!maps || maps.length == 0) {
            return q.when(false);
        }
            
        return getExistingCommentLinks()
        .then(function(data){
            data = data || {};
            maps.forEach(function(item){
                data[item.link] = item.number;
            });
            
            return data;
        })
        .then(function(data){
            var json = yaml.safeDump(data);
            return fs.write(dataFilePath, json);
        })
        .then(function(){
            return q.when(true);
        });
    };

    function main(GH_TOKEN){
        return getExistingCommentLinks()
        .then(function(data){
            return getUnlinkedPostFrontMatters(data);
        })
        .then(function(unlinkedFmAttrList){
            if(!unlinkedFmAttrList || unlinkedFmAttrList.length == 0)
                return;
            
            github.authenticate({
                type: "oauth",
                token: GH_TOKEN
            });
            
            var promiseArr = unlinkedFmAttrList.map(function(item){
                return createSingleIssue(item);
            });
            
            return q.all(promiseArr);
        })
        .then(function(commentMaps){
            return saveNewCommentLinks(commentMaps)
            .then(function(hasCreatedNewIssue){
                var message = hasCreatedNewIssue ? 
                                "Comment issues are created and linked successfully." : 
                                "No unlinked post found to create issue for commenting."
                console.log(message);
            });
        });
    }


    module.exports = main;

### **Add styling to your comments**

I have a `css` folder in my directory where I consolidate my styling files. Add a `comments.css` file to your folder of choice and add the following. 

    .comments{
        display: block;
    }

    .comments .comment{
        display: block;
        border: 1px solid lightgray;
        margin-top: 20px;
    }

    .comments .no-comments{
        margin-top: 20px;
    }

    .comment-header{
        display: block;
        background-color: darkgray;
        font-size: .9em;
    }

    .comment-header time{
        color: white;
        font-size: 0.8em;
        margin-left: 5px;
    }

    .comment-avatar {
        display: inline-block;
        height: 100%;
        margin: 5px;
    }

    .comment-avatar .dummy{
        display: inline-block;
        height: 100%;
        vertical-align: middle;
    }

    .comment-avatar img {
        vertical-align: middle;
        background-color: lightgray;
        height: 20px;
        width: 20px;
        border: 1px solid white;
    }

    .comment-body{
        margin-left: 5px;
    }

    .comment-body p{
        margin: 0px 5px;
        padding: 0px;
        font-size: 0.9em;
        line-height: 2em;
    }
    .video-container {
      position: relative;
      padding-bottom: 56.25%;
      padding-top: 30px; height: 0; overflow: hidden;
    }.video-container iframe,

    .video-container object,

    .video-container embed {
      position: absolute;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
    }
    .rss-container {
      position: relative;
      padding-bottom: 56.25%;
      padding-top: 30px; height: 0; overflow: hidden;
    }


The last thing you need to do is add the `include` function to your `post.html` file. I created a `post-no-comment.html` file as well for posts that do not need a comments section. 

`{% include comments.html %}`

That's it, now you should see the following render on your post.

![comments](https://richardbright.me/images/comments.png){:class="img-responsive"}

And once someone makes a comment...

![comment-test](https://richardbright.me/images/comment-test.png){:class="img-responsive"}