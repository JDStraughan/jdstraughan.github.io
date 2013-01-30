# JDStraughan.com

This is the source code to my personal blog.  Feel free to use it as a way to understand how I have implemented jekyll, liquid, plugins, etc.  Feel free to use any code, techniques, methods, or bad habits I have used.  If you find a bug, error, or bad habit that you can fix, please issue a pull request.  Lifting the site and making it your own without any attribution will result in bad karma.

## Where's the source?

GitHub Pages does not allow custom plugins for jekyll, so as a workaround, I am using a git pre-commit hook to move code around.  tl;dr: the source is in the _source directory.

To make a similar pre-commit hook, follow these directions that I kindly lifted from [here](https://github.com/mbenjaminsmith/mbenjaminsmith.github.com).

```bash
  $ mkdir _source
  $ mv _includes _layouts _posts css js img robots.txt 404.html index.html blog.html _source
  $ touch .git/hooks/pre-commit
  $ echo '#!/bin/sh' >> .git/hooks/pre-commit
  $ echo 'cp -rf ./_site/* ./' >> .git/hooks/pre-commit
  $ echo 'git add .' >> .git/hooks/pre-commit
  $ chmod +x .git/hooks/pre-commit
  $ echo 'source: ./_source' >> _config.yml
  $ echo 'plugins: ./_source/_plugins' >> _config.yml
```

Then just commit away and be happy.