# fjshonsoftware

## 新しい記事を書くとき

新しい記事を書くときのフローを示します。

```
git checkout -b article_slug
git branch
# Write new article on new directory
git add -A
git commit -m "Write article_slug"
git checkout main
git merge article_slug
git branch -d article_slug
git push
```

`git push`することで自動的にサーバー上のブログ記事に反映されます。
