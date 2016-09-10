# workshop2.github.io
To get this running, you will need 

- Ruby installed + path environment entry
- Bundle installed (`gem install bundle`)

Then run:
 - `bundle install`
   - This will install the packages required

To build and run the app, use:
```
jekyll serve --watch
```


Writing articles
----------------

Create your new page using:

```
bundle exec jekyll page "My New Page"
```

Create your new post using:

```
bundle exec jekyll post "My New Post"
```

Create

```
bundle exec jekyll draft "My new draft"
```

Publish your draft using:

```
bundle exec jekyll publish _drafts/my-new-draft.md
# or specify a specific date on which to publish it
bundle exec jekyll publish _drafts/my-new-draft.md --date 2014-01-24
```

Unpublish your post using:

```
bundle exec jekyll unpublish _posts/2014-01-24-my-new-draft.md
```