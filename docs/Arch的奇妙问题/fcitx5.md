在用gtk的界面库的软件上要下载fcitx5-gtk,否则打字可能会有一些问题,比如打yin的时候,只有yn而i已经输出到text的行上了.
patch:这里我发现fcitx-im包已经包含了fcitx5-gtk,但是我之前确实没下.
要下载xorg-xrdb
`echo "Xft.dpi: 144" | xrdb -merge `  
对于一些屏幕的缩放不能只在niri里面改  
还要在xwayland的相关设置也要改