# hexo notes

## setup

1. Install hexo

    `npm install -g hexo-cli`

2. Create a project

    `hexo init tee1365.github.io`

3. Run a test server

    `hexo server`

4. Create new branch

    create a new branch called source and checkout to it, we need to write things in this branch. The generated page will be on the master branch.

5. Set configuration

    ```md
    ~~~~~~~~~~~~~~~~~~ _config.yml ~~~~~~~~~~~~~~~~~~
    # Site
    title: tee1365's personal blog
    description: tee1365's personal blog
    author: tee1365

    # URL
    url: https://tee1365.github.io/
    ```

6. configure for deployment

    ```md
    npm install hexo-deployer-git --save
    ~~~~~~~~~~~~~~~~~~ _config.yml ~~~~~~~~~~~~~~~~~~
    # Deployment
    deploy:
      type: git
      repo: git@github.com:tee1365/tee1365.github.io.git
      branch: master
    ```

## commands

new post -> `hexo new title`

deploy -> `hexo generate -d` or `hexo deploy -g`
