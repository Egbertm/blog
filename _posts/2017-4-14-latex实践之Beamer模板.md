---
layout: post
title:  "latex实践之beamer模板"
date: 2017-4-14 11:59:52
categories:工具
tags:  latex 工具
---
* content
{:toc}  
  
对于包含大量公式的ppt,在排版上确实是挺耗时的，LaTeX提供了一套解决方案，也就是说
把ppt的那种帧格式做成pdf帧排版，虽然效果上比不上ppt的酷炫，但是对于排出来的公式
是传统ppt无法比拟的，下面即介绍如何用LaTeX beamer做PPT。




### 整体介绍

&emsp;&emsp;对于beamer而言，有一些专门的模板主题可供使用,可以在下面的链接中找到，  [beamer
theme](http://deic.uab.es/~iblanes/beamer_gallery/index_by_theme.html)  
对于主题而言beamer分的比较细，而且可以单独的搭配使用和变化，具体包括有内部主题
，外部主题，字体主题，颜色主题这几种。模板的使用也就是在这几种主题的搭配中体现
出来，而做成不一样风格的beamer

#### 外部主题
&emsp;&emsp;外部主题主要控制的是幻灯片顶部尾部的信息栏、边栏、图标、帧标题等一
帧之外的格式。预定义的外部主题有default、infolines、miniframes、smoothbars、
sidebar、split、shadow、tree、smoothtree等。

#### 颜色主题
&emsp;&emsp;关于颜色主题可以参考链接 [颜色主题
](http://deic.uab.es/~iblanes/beamer_gallery/index_by_color.html)。色彩主题控制
各个部分的色彩。预定义的色彩主题包括default、albatross、beaver、beetle、crane、
dolphine、dove、fly、lily、orchid、rose、seagull、seahorse、sidebartab、
structure、whale、wolverine等。

#### 字体主题

&emsp;&emsp;字体的主题可以参考链接 [字体主题
](http://deic.uab.es/~iblanes/beamer_gallery/index_by_font.html)
字体主题则控制幻灯片的整体字体风格。预定义的beamer字体主要包括default、
  professionalfonts、serif、structurebold、structureitalicserif、
  structuresmallcapsserif等。其中默认字体主题default的效果是整个幻灯片使用无衬
  线字体，这是多数幻灯片的选择；serif主题则是衬线字体，不过此时最好使用较大的字
  号和较粗的字体；professionalfonts不对字体有特别的设置，需要使用另外的专门的宏
包进行设置；structure开头的几个主题则对beamer中的几个结构有特别设置。
尤其是在排版上，对于一些数学字体不同的格式，美观等各个方面存在很大的差异，亲身
体会，使用professionalfonts排版得到的数学公式要比default的好看。而arev宏包，这个
宏包是专门为制作幻灯片设计的无衬线字体包，对正文字体和数学字体都有详细的调整，
因此不需要对beamer做额外的设置。只需要在导言
区加入如下代码即可：  
```language
\usebeamerfonttheme{professionalfonts}  
\usepackage{arev}
```
### 定理及公式的中文支持

&emsp;&emsp;在beamer中，已经预定义了许多定理类环境：theorem、corollary、definition，
definitions，fact，example以及examples，它们都以英文名称给出，例如theorem环境的
名臣就是“Theorem”。由于beamer调用了amsthm宏包定制定理类环境格式，因此也有用于证
明的proof环境。不过如果我们需要的是中文定理环境，则可以使用\newtheorem另行定义，
如： `\bewtheorem{thm}{定理}` 证明则用 `\renewcommand\proofname{证明}`。

### 图片与表格以及需要缩放的长公式

#### 插入图片

插入双排图片如下：
```latex
\begin{figure}[htbp]
  \centering
 {
        \begin{minipage}[h]{0.45\textwidth}
                    \centering
                    \includegraphics[width=0.9\textwidth]{Picture1.png}
                  \end{minipage}%
        }
       {
        \begin{minipage}[h]{0.45\textwidth}
                 \centering
                    \includegraphics[width=0.9\textwidth]{Picture2.png}
        \end{minipage}%
        }
        \rule{0pt}{0pt}
\end{figure}

```
效果如下图所示：  
![效果图](http://oqaij4yei.bkt.clouddn.com/2017_06_04_LaTeX_beamer_first.png "opt title")

#### 插入表格的化如下图

#### 插入长公式。
对于一些公式无法在一页中显示的时候我们需要进行缩放，因而下面介绍一些公式缩放的
例子，也就是把公式放在scalebox中如下：  
```latex
\scalebox{0.7}{
\parbox{\textwidth}{
\[\begin{gathered}
  {\left( \begin{gathered}
   - \lambda \;\;\;\;\;\;1 + 2\lambda \;\;\;\;\;\;\; - \lambda  \hfill \\
  \;\;\;\;\;\;\;\;\;\;\;\; - \lambda \;\;\;\;\;\;\;\;1 + 2\lambda \;\;\;\;\;\;\; - \lambda \;\;\;\; \hfill \\
  \;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\; - \lambda \;\;\;\;\;\;1 + 2\lambda \;\;\;\;\;\;\; - \lambda  \hfill \\
  \;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;...\;\;\;\;\;\;\;\;.... \hfill \\
  \;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\; - \lambda \;\;\;\;\;\;1 + 2\lambda \;\;\;\;\;\;\; - \lambda \; \hfill \\
\end{gathered}  \right)_{\left( {J - 1} \right) \times \left( {J + 1} \right)}}{\left[ \begin{gathered}
  u_{j,0}^{n + 1} \hfill \\
  u_{j,1}^{n + 1} \hfill \\
  u_{j,2}^{n + 1} \hfill \\
  ... \hfill \\
  u_{j,J - 1}^{n + 1} \hfill \\
  u_{j,J}^{n + 1} \hfill \\
\end{gathered}  \right]_{\left( {J + 1} \right) \times 1}} \hfill \\
   = {\left[ \begin{gathered}
  \lambda \;\;\;\;\;1 - 2\lambda  + \frac{\tau }{2}u_{jl}^{n + \frac{1}{2}}\left( {1 - C} \right)\;\;\;\;\;\lambda  \hfill \\
  \;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\lambda \;\;\;\;\;1 - 2\lambda  + \frac{\tau }{2}u_{jl}^{n + \frac{1}{2}}\left( {1 - C} \right)\;\;\;\;\;\lambda  \hfill \\
  \;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\lambda \;\;\;\;\;1 - 2\lambda  + \frac{\tau }{2}u_{jl}^{n + \frac{1}{2}}\left( {1 - C} \right)\;\;\;\;\;\lambda  \hfill \\
  \;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;...\;\;\;\;\;\;...\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\; \hfill \\
  \;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\lambda \;\;\;\;\;1 - 2\lambda  + \frac{\tau }{2}u_{jl}^{n + \frac{1}{2}}\left( {1 - C} \right)\;\;\;\;\;\lambda  \hfill \\
  \;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\lambda \;\;\;\;\;1 - 2\lambda  + \frac{\tau }{2}u_{jl}^{n + \frac{1}{2}}\left( {1 - C} \right)\;\;\;\;\;\lambda  \hfill \\
\end{gathered}  \right]_{\left( {J - 1} \right) \times \left( {J + 1} \right)}}{\left[ \begin{gathered}
  u_{0,l}^{n{\text{ + }}\frac{1}{2}} \hfill \\
  u_{1,l}^{n{\text{ + }}\frac{1}{2}} \hfill \\
  u_{2,l}^{n{\text{ + }}\frac{1}{2}} \hfill \\
  ... \hfill \\
  u_{J - 1,l}^{n{\text{ + }}\frac{1}{2}} \hfill \\
  u_{J,l}^{n{\text{ + }}\frac{1}{2}} \hfill \\
\end{gathered}  \right]_{\left( {J + 1} \right) \times 1}}\;\;\;\;\left( {10} \right) \hfill \\
\end{gathered} \]
}
}
```
效果图如下图所示：  
![长公式效果图
](http://oqaij4yei.bkt.clouddn.com/2017_06_04_LaTeX_beamer_second.png "opt title").

#### 复杂表格处理。
复杂表格的绘制涉及的一些跨栏，跨行的表格，下面介绍一个简单的例子如下：  
```language
        \begin{center}
          \captionof{table}{预测的分类结果}\label{tab:tabset2}
      %    \caption{示例表格}\label{tab:test}
          \begin{tabular}{cccc}
            \toprule
            \multirow{2}{*}{比较指标}& \multirow{2}{*}{数据集} &
            \multicolumn{2}{c}{KNN算法} \\
            \cline{3-4}
         & & DIEIC & 改进的DIEIC \\
            \midrule
            \multirow{4}{*}{预测精度\%}& D\_Set\_1 & 66.48 & 67.34 \\
            & D\_Set\_2 & 66.33 & 67.42 \\
            & D\_Set\_3 & 66.35& 67.30 \\
            & D\_Set1 & 66.45 & 67.21 \\
            \bottomrule
          \end{tabular}
        \end{center}
```

其效果图如下图所示：  
![表格效果图](http://oqaij4yei.bkt.clouddn.com/2017_06_04_LaTeX_beamer_third.png "表格")  

至此已经可以完成简单的beamer制作了，以下附一个简单的beamer模板。
```language
\documentclass[xcolor=svgnames]{beamer}
\usepackage{txfonts}
\usepackage{amsmath}
\usepackage{times}
\usepackage{mathptmx}
\usefonttheme{professionalfonts}
\usecolortheme[named=PowderBlue]
{structure} %structure
\useoutertheme{infolines}
\usetheme[height=7mm]{Madrid} %Rochester
\setbeamertemplate{items}[ball]
\setbeamertemplate{blocks}[rounded][shadow=true]
\setbeamertemplate{navigation symbols}{}
\setbeamertemplate{background canvas}[vertical shading][left=PowderBlue!10,middle=white,right=PowderBlue!10]
\newcommand\diff{\,{\mathrm d}}
\usepackage{xeCJK}
\setCJKmainfont{SimSun}
\usepackage{tikz}
\usetikzlibrary{shapes,arrows}
\hypersetup{ pdfpagemode={FullScreen}}
\usepackage{graphicx}
\usepackage{subfigure}
\subtitle{中国梦}
\author{×××××\\×××××××}
\institute[BUCT]{
  中华大学\\
  中国·北京
}
\date{2017年5月1日}
\begin{document}
\begin{frame}[plain]
  \titlepage
\end{frame}
\begin{frame}{模板}
beamer模板
\end{frame}
\end{document}
```
