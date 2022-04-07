## Why

https://github.com/neovim/neovim/issues/18017

And Vim's doc is too complicated to understand at a short time, 
so everyone can benefit from taking his own notes and have his own version of vimdoc.
Here is my version of vimdoc for neovim 0.6.1

## Usage
Put all my files to the directory below (or alike)  
overwriting the original files:
/home/linuxbrew/.linuxbrew/Cellar/neovim/0.6.1/share/nvim/runtime/doc/


Keep the .git directory there too, 
so that you can sync your own version of vimdoc  across machines, and rollback.


To enable taking notes in vimdoc,
I modified the official ftplugin/help.vim and syntax/help.vim

Find them in cfg/nvim/ftplugin/help.vim from 
https://gitee.com/llwwff/dotF.git


## See also:

如何高效使用vim的help文档/手册(vimdoc)或改进? - kidneyball的回答 - 知乎
https://www.zhihu.com/question/522622970/answer/2402454732


如何将长文本, 按意群(成分)断句或换行, 转化为更容易理解, 更适合放进PPT的列表? - 胆大路野的回答 - 知乎
https://www.zhihu.com/question/522644260/answer/2395778565
