---
title: UT for front-end
date: 2017-11-14 13:29:31
categories:
- coding
- font-end
tags: UT
---

前段项目的UT一般可以采用karma+jasmine来搭建。jasmine做单元测试，karma自动化完成单元测试。
现在前段项目越来越多的使用webpack进行打包，所以karma-webpack不可或缺。
与此同时，因为webpack会把js打包成一个文件，因此如果要让karma生成coverage report就额外的第三方的js库。
<!--more-->

### coverage reporter

依赖的package：
- istanbul-instrumenter-loader
- karma-coverage
- karma-remap-coverage
- karma-sourcemap-loader
- karma-webpack

要生成coverage reporter，首先在karma.config.js中设置webpackconfig文件以及remap-converage

```javescript
module.exports = function (config) {
    config.set({
        ...,
        webpack: require('./path/to/webpack.test.js'),
        ...,

        reporters: ['mocha', 'coverage', 'remap-coverage'],
        coverageReporter: {
            type: 'in-memory'
        },
        remapCoverageReporter: {
            'text-summary': null,
            json: './coverage/coverage.json',
            html: './coverage/html'
        }

    });
}
```

在webpack.config.js中配置需要的加载器，例如css-loader, scss-loader, typescript-loader以及`istanbul-instrumenter-loader`
```
module.exports = {
    devtools: 'inline-source-map',
    ...,

    modules: {
        rules: [
            ...,
                /**
             * Source map loader support for *.js files
             * Extracts SourceMaps for source files that as added as sourceMappingURL comment.
             *
             * See: https://github.com/webpack/source-map-loader
             */
            {
              enforce: 'pre',
              test: /\.js$/,
              loader: 'source-map-loader',
              exclude: [
                // these packages have problems with their sourcemaps
                path.join(basePath, 'node_modules/rxjs'),
                path.join(basePath, 'node_modules/@angular')
              ]
            },
            /**
             * Instruments JS files with Istanbul for subsequent code coverage reporting.
             * Instrument only testing sources.
             *
             * See: https://github.com/deepsweet/istanbul-instrumenter-loader
             */
            {
              enforce: 'post',
              test: /\.(js|ts)$/,
              loader: 'istanbul-instrumenter-loader',
              include: path.resolve(__dirname, 'path'),
              exclude: [
                /\.(e2e|spec)\.ts$/,
                /node_modules/
              ]
            }
        ]
    },
    ...
}
```