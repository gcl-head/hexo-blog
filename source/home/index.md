---
title: home
type: ''
date: 2021-01-04 17:19:33
---
## Introduction

Hello, this is a [hexo](https://hexo.io/) blog Using Vesselin Tzvetkov's [solar-theme](https://github.com/tzvetkov75/solar-theme-hexo). I will share my study and life here. Hope you can find what you want here and have fun.

Actually, this is my second personal blog. My last blog, a big curde through, can also be called minimalism, is based on Vue and Django with the idea of learning web development technology. If interested, you can visit it through [bighead.net.cn](https://bighead.net.cn/), or if the website cannot be accessed, you can directly see the code by checking [github.com/gcl-head/blog](https://github.com/gcl-head/blog).

## My everyday
<div style="background: black;padding: 9px 8px 0px 8px; width: 30vw;
margin: 3rem auto 0;">
    <div style="font-size: larger;">
        <span style="color: #f501ef; ">void</span><span style="color: #00ff06; "> everyday_work</span> () {
        <div style="margin-left: 2em;">
            <span style="color: #f501ef; ">if</span> (is_alive()) {
              <div style="margin-left: 2em;">
                eat();<br/>
                sleep();<br/>
                code();<br/>
                everyday_work();
              </div>
            }<br/>
            <span id="lastLine">
                <span style="color: #f501ef;">return</span>;
            </span>
        </div>
        }
    </div>
</div>

<style>
    #lastLine{
        position: relative;
    }
     
    #lastLine:after {
        position: absolute;
        content: '';
        display: inline-block;
        width: 9px;
        height: 18px;
        top: 50%;
        transform: translateY(-50%);
        animation: blink 1.2s infinite steps(1, start);
    }
     
    @keyframes blink {
        0%, 100% {
            background-color: white;
        }
        50% {
            background-color: transparent;
        } 
    }
</style>