
## Creating a new entry in hexo:

To create a new post or a new page, you can run the following command:

```
hexo new [layout] <title>
```

post is the default layout, but you can supply your own. You can change the default layout by editing the default_layout setting in _config.yml.

Example:
```
hexo new draft docker_node_env_var
```
NOTE: draft is a special kind of post... it will land in the __\_drafts__ folder.

## deleting a post in Hexo:
There is no command to delete a post on Hexo, but follow this steps :

1. Delete the post under source/_post folder
2. Run ```hexo clean``` to delete the database (db.json) and assets folder
3. Run ```hexo generate``` to generate the new blog without your deleted post

## Releasing a draft:

```
hexo publish [layout] <title>
```

Example:
```
hexo publish docker_node_env_var
```

## Showing the website:
```
hexo server
```