## npm

`npm` 是 npm CLI 客户端程序提供的主命令。在 Terminal 中运行 `npm` 命令可以查看所有的子命令、配置文件位置以及客户端的安装目录(prefix)。

 ```shell
    ➜  ~ npm
    npm <command>
    
    # 使用示例
    Usage:
    
    npm install        install all the dependencies in your project
    npm install <foo>  add the <foo> dependency to your project
    npm test           run this projects tests
    npm run <foo>      run the script named <foo>
    npm <command> -h   quick help on <command>
    
    # 所有下级命令
    All commands:
    
        access, adduser, audit, bin, bugs, cache, ci, completion,
        config, dedupe, deprecate, diff, dist-tag, docs, doctor,
        edit, exec, explain, explore, find-dupes, fund, get, help,
        hook, init, install, install-ci-test, install-test, link,
        ll, login, logout, ls, org, outdated, owner, pack, ping,
        pkg, prefix, profile, prune, publish, query, rebuild, repo,
        restart, root, run-script, search, set, set-script,
        shrinkwrap, star, stars, start, stop, team, test, token,
        uninstall, unpublish, unstar, update, version, view, whoami
    
    # 指定配置的方式（通过 .npmrc 的配置文件或命令行中是有命令参数）
    Specify configs in the ini-formatted file:
        /Users/xxxx/.npmrc
    or on the command line via: npm <command> --key=value
    
    # npm 程序的安装位置，这里使用 nvm 包管理器安装。
    npm@8.19.2 /Users/xxxx/.nvm/versions/node/v16.18.1/lib/node_modules/npm
```

## npx

运行 npm 包的命令。在 npm 7.x 版本中，`npx` 命令已被 [[29 × NPM CLI - exec]] 替代。 