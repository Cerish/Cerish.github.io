---
title: 记录 vue 的 axios 拦截配置
date: 2019-10-08 12:26:11
tags: vue
---

在vue 的 api 请求中，需要先解决跨域的问题，在vue-cli3中的解决办法，是在根目录下，建立一个 vue.config.js 文件，添加如下配置

    module.exports = {
        port: 8080, // 端口设置
        host: "0.0.0.0", // 局域网访问配置
        devServer: {
            proxy: {
                '/api': {
                    target: 'http://xxxx/device/', //对应自己的接口
                    changeOrigin: true,
                    ws: true,
                    pathRewrite: {
                    '^/api': ''
                    }
                }
            }
        }
    }

接下来，在vue 项目的请求中，一般是搭配 axios 配置进行api请求访问。进行如下设置，可对请求进行request拦截与 response 拦截。

    import axios from 'axios'; 引入 axios
    import router from '../router.js'; 引入router,可进行跳转
    // 请求头，设置公共参
    // const comm_req = {
    //   id: '123',
    //   phonno: '123'
    // }
    // 创建axios实例
    const axiosServer = axios.create({
        baseURL: '/',
        timeout: 50000 // 请求超时时间
    })
    // 拦截请求
    axiosServer.interceptors.request.use(config => {
        // 查看是否具有token，并写在request header 里面
        if (window.localStorage.getItem('token')) {
            config.headers.common['Authorization'] =  window.localStorage.getItem('token')
        }
        return config
        }, error => {
        console.log('进入请求错误' + error)
        return Promise.reject(error)
    })
    // 拦截响应
    axiosServer.interceptors.response.use(response => {
        if (response.headers.authorization) {
            localStorage.token = response.headers.authorization;
        }
        return response.data
    }, error => {
        if (error.response) {
            switch (error.response.status) {
            case 401:
                console.log('401错误');
                router.replace({
                path: '/login'
                })
                break;
            case 404:
                console.log('404网址错误');
                break;  
            }
        }
        return Promise.reject(error);
    })
    export default axiosServer;

记录一下，在获取到 response 错误的时候， 直接打印 error， 会出现以下的错误

    Error: Request failed with status code 401
        at createError (createError.js?2d83:16)
        at settle (settle.js?467f:17)
        at XMLHttpRequest.handleLoad (xhr.js?b50d:59)

记得使用 error.response， 一般情况下，返回的消息都存放在 rror.response.data 里面。