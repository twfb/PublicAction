[![Deploy](https://github.com/twfb/PublicActions/actions/workflows/deploy.yml/badge.svg)](https://github.com/twfb/PublicActions/actions/workflows/deploy.yml)

[![Blog](https://github.com/twfb/PublicActions/actions/workflows/blog.yml/badge.svg)](https://github.com/twfb/PublicActions/actions/workflows/blog.yml)

- Blog: 博客更新
- Deploy: 博客+个人脚本执行

示例: blog.yml
```
name: Blog
on:
  workflow_dispatch:
env:
  TOKEN: ${{ secrets.TOKEN }}
# A workflow run is made up of one or more jobs that can run sequentially or in parallel
jobs:
  # This workflow contains a single job called "build"
  build:
    # The type of runner that the job will run on
    runs-on: ubuntu-latest

    # Steps represent a sequence of tasks that will be executed as part of the job
    steps:
      - uses: actions/setup-node@v3
      - name: deploy
        run: |
          mkdir -p ~/.ssh/
          echo  "${{secrets.SSH_PRI}}" > ~/.ssh/id_rsa
          chmod 600 ~/.ssh/id_rsa
          ssh-keyscan github.com >> ~/.ssh/known_hosts
          git config --global user.name ${{secrets.USERNAME}}
          git config --global user.email ${{secrets.EMAIL}}
          git clone git@github.com:${{secrets.USERNAME}}/${{secrets.DEPLOY_REPO}}.git
          cd ${{secrets.DEPLOY_REPO}}
          sh blog.sh > /dev/null 2>&1

```

示例: blog.sh
```
git submodule update --init --recursive --remote
cp cactus_config.yml themes/cactus/_config.yml

npm i
npm run build
npm run deploy
```

这样就无法在网页上查看日志了, 可以通过生成日志文件到仓库里, 然后提交查看, 例: `sh blog.sh > log/blog.log 2>&1`, `npm run deploy > log/deploy.log 2>&1`

想自动执行? 两种方法
- 设置计划
- 私有仓库中的action中通过api调用执行公开仓库下的action, [api文档](https://docs.github.com/cn/rest/actions/workflows#create-a-workflow-dispatch-event)
