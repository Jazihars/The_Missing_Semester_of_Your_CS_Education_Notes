# 如何高效地使用你的电脑？｜麻省理工学院《计算机教育中缺失的一课》（3）——Vim文本编辑器
在这个笔记中，我将记录麻省理工学院著名的 **《计算机教育中缺失的一课》** 的第3课的学习笔记。**这门课的主要目的在于，教你如何高效地使用计算机中你常用的各种工具**。众所周知，计算机已经深入了我们生活的方方面面。但是，你不一定真的会**高效地**使用它。这门课的目的就在于，教你如何高效地使用工具，帮助你高效地使用计算机。第3节课的内容是**Vim文本编辑器**。

[2021年的Stack Overflow开发者调查](https://insights.stackoverflow.com/survey/2021#section-most-popular-technologies-integrated-development-environment)显示，**vscode**是最受欢迎的开发环境，82000多名参加调查的开发者中有71.06%的人表示他们使用过vscode。的确，vscode是我目前主要使用的开发工具，我也坚定认为，vscode是目前最好用的文本编辑工具。但是，在Linux系统上，我们仍然有必要掌握Vim。因此，接下来我们就来学习Vim的使用方法。之后绝大多数时候，我将继续使用vscode在Windows和Linux操作系统上编辑文本，只在必要且方便的时候使用Vim。

在之前的课程笔记中已经说了，我是使用vscode的Remote-SSH来远程连接Linux机器的。因此，我会在vscode的Linux终端里启用Vim文本编辑器。远程SSH连接Linux机器后，打开vscode终端，输入如下的命令：
``` bash
vim --version
```
Linux终端里输出了如下的内容：
```
VIM - Vi IMproved 7.4 (2013 Aug 10, compiled Dec 15 2020 16:44:08)
Included patches: 1-207, 209-629
Modified by <bugzilla@redhat.com>
Compiled by <bugzilla@redhat.com>
Huge version without GUI.  Features included (+) or not (-):
+acl             +farsi           +mouse_netterm   +syntax
+arabic          +file_in_path    +mouse_sgr       +tag_binary
+autocmd         +find_in_path    -mouse_sysmouse  +tag_old_static
-balloon_eval    +float           +mouse_urxvt     -tag_any_white
-browse          +folding         +mouse_xterm     -tcl
++builtin_terms  -footer          +multi_byte      +terminfo
+byte_offset     +fork()          +multi_lang      +termresponse
+cindent         +gettext         -mzscheme        +textobjects
-clientserver    -hangul_input    +netbeans_intg   +title
-clipboard       +iconv           +path_extra      -toolbar
+cmdline_compl   +insert_expand   +perl            +user_commands
+cmdline_hist    +jumplist        +persistent_undo +vertsplit
+cmdline_info    +keymap          +postscript      +virtualedit
+comments        +langmap         +printer         +visual
+conceal         +libcall         +profile         +visualextra
+cryptv          +linebreak       +python/dyn      +viminfo
+cscope          +lispindent      -python3         +vreplace
+cursorbind      +listcmds        +quickfix        +wildignore
+cursorshape     +localmap        +reltime         +wildmenu
+dialog_con      -lua             +rightleft       +windows
+diff            +menu            +ruby/dyn        +writebackup
+digraphs        +mksession       +scrollbind      -X11
-dnd             +modify_fname    +signs           -xfontset
-ebcdic          +mouse           +smartindent     -xim
+emacs_tags      -mouseshape      -sniff           -xsmp
+eval            +mouse_dec       +startuptime     -xterm_clipboard
+ex_extra        +mouse_gpm       +statusline      -xterm_save
+extra_search    -mouse_jsbterm   -sun_workshop    -xpm
   system vimrc file: "/etc/vimrc"
     user vimrc file: "$HOME/.vimrc"
 2nd user vimrc file: "~/.vim/vimrc"
      user exrc file: "$HOME/.exrc"
  fall-back for $VIM: "/etc"
 f-b for $VIMRUNTIME: "/usr/share/vim/vim74"
Compilation: gcc -c -I. -Iproto -DHAVE_CONFIG_H     -O2 -g -pipe -Wall -fexceptions -fstack-protector-strong --param=ssp-buffer-size=4 -grecord-gcc-switches   -m64 -mtune=generic -D_GNU_SOURCE -D_FILE_OFFSET_BITS=64 -D__linux__ -D_REENTRANT -U_FORTIFY_SOURCE -D_FORTIFY_SOURCE=1      
Linking: gcc   -L. -Wl,-z,relro -fstack-protector -rdynamic -Wl,-export-dynamic -Wl,--enable-new-dtags -Wl,-rpath,/usr/lib64/perl5/CORE  -Wl,-z,relro  -L/usr/local/lib -Wl,--as-needed -o vim        -lm -lnsl  -lselinux  -lncurses -lacl -lattr -lgpm -ldl   -Wl,--enable-new-dtags -Wl,-rpath,/usr/lib64/perl5/CORE  -fstack-protector  -L/usr/lib64/perl5/CORE -lperl -lresolv -lnsl -ldl -lm -lcrypt -lutil -lpthread -lc
```
这就是我使用的Linux机器上的Vim版本。在这节课里，我将记录Vim的用法。

## Vim的哲学
参考[计算机教育中缺失的一课第3课编辑器(Vim)](https://missing-semester-cn.github.io/2020/editors/)的内容，vim的哲学如下：
>在编程的时候，你会把大量时间花在阅读/编辑而不是在写代码上。所以，Vim 是一个_多模态_编辑 器：它对于插入文字和操纵文字有不同的模式。Vim 是可编程的（可以使用 Vimscript 或者像 Python 一样的其他程序语言），Vim 的接口本身也是一个程序语言：键入操作（以及其助记名） 是命令，这些命令也是可组合的。Vim 避免了使用鼠标，因为那样太慢了；Vim 甚至避免用 上下左右键因为那样需要太多的手指移动。
>
>这样的设计哲学使得 Vim 成为了一个能跟上你思维速度的编辑器。

熟练掌握vim，可以加速编写代码的速度，对于一般的文本编辑任务也很有益处。参见[1700页数学笔记火了！全程敲代码，速度飞快易搜索，硬核小哥教你上手LaTeX+Vim](https://zhuanlan.zhihu.com/p/60049290) 和 [世界上最好的编辑器Vim：1700多页数学笔记是如何实时完成的](https://zhuanlan.zhihu.com/p/61036165)。**因此，花时间打磨我们对工具的熟练使用是绝对值得的，未来一定能节省不少时间。**

## 开始使用vim
在`mydir`目录下，在Linux终端里使用`touch mytest.py`命令创建一个空白脚本`mytest.py`，然后将下述[Swin Transformer代码](https://github.com/microsoft/Swin-Transformer/blob/main/models/swin_transformer.py)复制到`mytest.py`中：
``` python
# --------------------------------------------------------
# Swin Transformer
# Copyright (c) 2021 Microsoft
# Licensed under The MIT License [see LICENSE for details]
# Written by Ze Liu
# --------------------------------------------------------

import torch
import torch.nn as nn
import torch.utils.checkpoint as checkpoint
from timm.models.layers import DropPath, to_2tuple, trunc_normal_


class Mlp(nn.Module):
    def __init__(self, in_features, hidden_features=None, out_features=None, act_layer=nn.GELU, drop=0.):
        super().__init__()
        out_features = out_features or in_features
        hidden_features = hidden_features or in_features
        self.fc1 = nn.Linear(in_features, hidden_features)
        self.act = act_layer()
        self.fc2 = nn.Linear(hidden_features, out_features)
        self.drop = nn.Dropout(drop)

    def forward(self, x):
        x = self.fc1(x)
        x = self.act(x)
        x = self.drop(x)
        x = self.fc2(x)
        x = self.drop(x)
        return x


def window_partition(x, window_size):
    """
    Args:
        x: (B, H, W, C)
        window_size (int): window size
    Returns:
        windows: (num_windows*B, window_size, window_size, C)
    """
    B, H, W, C = x.shape
    x = x.view(B, H // window_size, window_size, W // window_size, window_size, C)
    windows = x.permute(0, 1, 3, 2, 4, 5).contiguous().view(-1, window_size, window_size, C)
    return windows


def window_reverse(windows, window_size, H, W):
    """
    Args:
        windows: (num_windows*B, window_size, window_size, C)
        window_size (int): Window size
        H (int): Height of image
        W (int): Width of image
    Returns:
        x: (B, H, W, C)
    """
    B = int(windows.shape[0] / (H * W / window_size / window_size))
    x = windows.view(B, H // window_size, W // window_size, window_size, window_size, -1)
    x = x.permute(0, 1, 3, 2, 4, 5).contiguous().view(B, H, W, -1)
    return x


class WindowAttention(nn.Module):
    r""" Window based multi-head self attention (W-MSA) module with relative position bias.
    It supports both of shifted and non-shifted window.
    Args:
        dim (int): Number of input channels.
        window_size (tuple[int]): The height and width of the window.
        num_heads (int): Number of attention heads.
        qkv_bias (bool, optional):  If True, add a learnable bias to query, key, value. Default: True
        qk_scale (float | None, optional): Override default qk scale of head_dim ** -0.5 if set
        attn_drop (float, optional): Dropout ratio of attention weight. Default: 0.0
        proj_drop (float, optional): Dropout ratio of output. Default: 0.0
    """

    def __init__(self, dim, window_size, num_heads, qkv_bias=True, qk_scale=None, attn_drop=0., proj_drop=0.):

        super().__init__()
        self.dim = dim
        self.window_size = window_size  # Wh, Ww
        self.num_heads = num_heads
        head_dim = dim // num_heads
        self.scale = qk_scale or head_dim ** -0.5

        # define a parameter table of relative position bias
        self.relative_position_bias_table = nn.Parameter(
            torch.zeros((2 * window_size[0] - 1) * (2 * window_size[1] - 1), num_heads))  # 2*Wh-1 * 2*Ww-1, nH

        # get pair-wise relative position index for each token inside the window
        coords_h = torch.arange(self.window_size[0])
        coords_w = torch.arange(self.window_size[1])
        coords = torch.stack(torch.meshgrid([coords_h, coords_w]))  # 2, Wh, Ww
        coords_flatten = torch.flatten(coords, 1)  # 2, Wh*Ww
        relative_coords = coords_flatten[:, :, None] - coords_flatten[:, None, :]  # 2, Wh*Ww, Wh*Ww
        relative_coords = relative_coords.permute(1, 2, 0).contiguous()  # Wh*Ww, Wh*Ww, 2
        relative_coords[:, :, 0] += self.window_size[0] - 1  # shift to start from 0
        relative_coords[:, :, 1] += self.window_size[1] - 1
        relative_coords[:, :, 0] *= 2 * self.window_size[1] - 1
        relative_position_index = relative_coords.sum(-1)  # Wh*Ww, Wh*Ww
        self.register_buffer("relative_position_index", relative_position_index)

        self.qkv = nn.Linear(dim, dim * 3, bias=qkv_bias)
        self.attn_drop = nn.Dropout(attn_drop)
        self.proj = nn.Linear(dim, dim)
        self.proj_drop = nn.Dropout(proj_drop)

        trunc_normal_(self.relative_position_bias_table, std=.02)
        self.softmax = nn.Softmax(dim=-1)

    def forward(self, x, mask=None):
        """
        Args:
            x: input features with shape of (num_windows*B, N, C)
            mask: (0/-inf) mask with shape of (num_windows, Wh*Ww, Wh*Ww) or None
        """
        B_, N, C = x.shape
        qkv = self.qkv(x).reshape(B_, N, 3, self.num_heads, C // self.num_heads).permute(2, 0, 3, 1, 4)
        q, k, v = qkv[0], qkv[1], qkv[2]  # make torchscript happy (cannot use tensor as tuple)

        q = q * self.scale
        attn = (q @ k.transpose(-2, -1))

        relative_position_bias = self.relative_position_bias_table[self.relative_position_index.view(-1)].view(
            self.window_size[0] * self.window_size[1], self.window_size[0] * self.window_size[1], -1)  # Wh*Ww,Wh*Ww,nH
        relative_position_bias = relative_position_bias.permute(2, 0, 1).contiguous()  # nH, Wh*Ww, Wh*Ww
        attn = attn + relative_position_bias.unsqueeze(0)

        if mask is not None:
            nW = mask.shape[0]
            attn = attn.view(B_ // nW, nW, self.num_heads, N, N) + mask.unsqueeze(1).unsqueeze(0)
            attn = attn.view(-1, self.num_heads, N, N)
            attn = self.softmax(attn)
        else:
            attn = self.softmax(attn)

        attn = self.attn_drop(attn)

        x = (attn @ v).transpose(1, 2).reshape(B_, N, C)
        x = self.proj(x)
        x = self.proj_drop(x)
        return x

    def extra_repr(self) -> str:
        return f'dim={self.dim}, window_size={self.window_size}, num_heads={self.num_heads}'

    def flops(self, N):
        # calculate flops for 1 window with token length of N
        flops = 0
        # qkv = self.qkv(x)
        flops += N * self.dim * 3 * self.dim
        # attn = (q @ k.transpose(-2, -1))
        flops += self.num_heads * N * (self.dim // self.num_heads) * N
        #  x = (attn @ v)
        flops += self.num_heads * N * N * (self.dim // self.num_heads)
        # x = self.proj(x)
        flops += N * self.dim * self.dim
        return flops


class SwinTransformerBlock(nn.Module):
    r""" Swin Transformer Block.
    Args:
        dim (int): Number of input channels.
        input_resolution (tuple[int]): Input resulotion.
        num_heads (int): Number of attention heads.
        window_size (int): Window size.
        shift_size (int): Shift size for SW-MSA.
        mlp_ratio (float): Ratio of mlp hidden dim to embedding dim.
        qkv_bias (bool, optional): If True, add a learnable bias to query, key, value. Default: True
        qk_scale (float | None, optional): Override default qk scale of head_dim ** -0.5 if set.
        drop (float, optional): Dropout rate. Default: 0.0
        attn_drop (float, optional): Attention dropout rate. Default: 0.0
        drop_path (float, optional): Stochastic depth rate. Default: 0.0
        act_layer (nn.Module, optional): Activation layer. Default: nn.GELU
        norm_layer (nn.Module, optional): Normalization layer.  Default: nn.LayerNorm
    """

    def __init__(self, dim, input_resolution, num_heads, window_size=7, shift_size=0,
                 mlp_ratio=4., qkv_bias=True, qk_scale=None, drop=0., attn_drop=0., drop_path=0.,
                 act_layer=nn.GELU, norm_layer=nn.LayerNorm):
        super().__init__()
        self.dim = dim
        self.input_resolution = input_resolution
        self.num_heads = num_heads
        self.window_size = window_size
        self.shift_size = shift_size
        self.mlp_ratio = mlp_ratio
        if min(self.input_resolution) <= self.window_size:
            # if window size is larger than input resolution, we don't partition windows
            self.shift_size = 0
            self.window_size = min(self.input_resolution)
        assert 0 <= self.shift_size < self.window_size, "shift_size must in 0-window_size"

        self.norm1 = norm_layer(dim)
        self.attn = WindowAttention(
            dim, window_size=to_2tuple(self.window_size), num_heads=num_heads,
            qkv_bias=qkv_bias, qk_scale=qk_scale, attn_drop=attn_drop, proj_drop=drop)

        self.drop_path = DropPath(drop_path) if drop_path > 0. else nn.Identity()
        self.norm2 = norm_layer(dim)
        mlp_hidden_dim = int(dim * mlp_ratio)
        self.mlp = Mlp(in_features=dim, hidden_features=mlp_hidden_dim, act_layer=act_layer, drop=drop)

        if self.shift_size > 0:
            # calculate attention mask for SW-MSA
            H, W = self.input_resolution
            img_mask = torch.zeros((1, H, W, 1))  # 1 H W 1
            h_slices = (slice(0, -self.window_size),
                        slice(-self.window_size, -self.shift_size),
                        slice(-self.shift_size, None))
            w_slices = (slice(0, -self.window_size),
                        slice(-self.window_size, -self.shift_size),
                        slice(-self.shift_size, None))
            cnt = 0
            for h in h_slices:
                for w in w_slices:
                    img_mask[:, h, w, :] = cnt
                    cnt += 1

            mask_windows = window_partition(img_mask, self.window_size)  # nW, window_size, window_size, 1
            mask_windows = mask_windows.view(-1, self.window_size * self.window_size)
            attn_mask = mask_windows.unsqueeze(1) - mask_windows.unsqueeze(2)
            attn_mask = attn_mask.masked_fill(attn_mask != 0, float(-100.0)).masked_fill(attn_mask == 0, float(0.0))
        else:
            attn_mask = None

        self.register_buffer("attn_mask", attn_mask)

    def forward(self, x):
        H, W = self.input_resolution
        B, L, C = x.shape
        assert L == H * W, "input feature has wrong size"

        shortcut = x
        x = self.norm1(x)
        x = x.view(B, H, W, C)

        # cyclic shift
        if self.shift_size > 0:
            shifted_x = torch.roll(x, shifts=(-self.shift_size, -self.shift_size), dims=(1, 2))
        else:
            shifted_x = x

        # partition windows
        x_windows = window_partition(shifted_x, self.window_size)  # nW*B, window_size, window_size, C
        x_windows = x_windows.view(-1, self.window_size * self.window_size, C)  # nW*B, window_size*window_size, C

        # W-MSA/SW-MSA
        attn_windows = self.attn(x_windows, mask=self.attn_mask)  # nW*B, window_size*window_size, C

        # merge windows
        attn_windows = attn_windows.view(-1, self.window_size, self.window_size, C)
        shifted_x = window_reverse(attn_windows, self.window_size, H, W)  # B H' W' C

        # reverse cyclic shift
        if self.shift_size > 0:
            x = torch.roll(shifted_x, shifts=(self.shift_size, self.shift_size), dims=(1, 2))
        else:
            x = shifted_x
        x = x.view(B, H * W, C)
        x = shortcut + self.drop_path(x)

        # FFN
        x = x + self.drop_path(self.mlp(self.norm2(x)))

        return x

    def extra_repr(self) -> str:
        return f"dim={self.dim}, input_resolution={self.input_resolution}, num_heads={self.num_heads}, " \
               f"window_size={self.window_size}, shift_size={self.shift_size}, mlp_ratio={self.mlp_ratio}"

    def flops(self):
        flops = 0
        H, W = self.input_resolution
        # norm1
        flops += self.dim * H * W
        # W-MSA/SW-MSA
        nW = H * W / self.window_size / self.window_size
        flops += nW * self.attn.flops(self.window_size * self.window_size)
        # mlp
        flops += 2 * H * W * self.dim * self.dim * self.mlp_ratio
        # norm2
        flops += self.dim * H * W
        return flops


class PatchMerging(nn.Module):
    r""" Patch Merging Layer.
    Args:
        input_resolution (tuple[int]): Resolution of input feature.
        dim (int): Number of input channels.
        norm_layer (nn.Module, optional): Normalization layer.  Default: nn.LayerNorm
    """

    def __init__(self, input_resolution, dim, norm_layer=nn.LayerNorm):
        super().__init__()
        self.input_resolution = input_resolution
        self.dim = dim
        self.reduction = nn.Linear(4 * dim, 2 * dim, bias=False)
        self.norm = norm_layer(4 * dim)

    def forward(self, x):
        """
        x: B, H*W, C
        """
        H, W = self.input_resolution
        B, L, C = x.shape
        assert L == H * W, "input feature has wrong size"
        assert H % 2 == 0 and W % 2 == 0, f"x size ({H}*{W}) are not even."

        x = x.view(B, H, W, C)

        x0 = x[:, 0::2, 0::2, :]  # B H/2 W/2 C
        x1 = x[:, 1::2, 0::2, :]  # B H/2 W/2 C
        x2 = x[:, 0::2, 1::2, :]  # B H/2 W/2 C
        x3 = x[:, 1::2, 1::2, :]  # B H/2 W/2 C
        x = torch.cat([x0, x1, x2, x3], -1)  # B H/2 W/2 4*C
        x = x.view(B, -1, 4 * C)  # B H/2*W/2 4*C

        x = self.norm(x)
        x = self.reduction(x)

        return x

    def extra_repr(self) -> str:
        return f"input_resolution={self.input_resolution}, dim={self.dim}"

    def flops(self):
        H, W = self.input_resolution
        flops = H * W * self.dim
        flops += (H // 2) * (W // 2) * 4 * self.dim * 2 * self.dim
        return flops


class BasicLayer(nn.Module):
    """ A basic Swin Transformer layer for one stage.
    Args:
        dim (int): Number of input channels.
        input_resolution (tuple[int]): Input resolution.
        depth (int): Number of blocks.
        num_heads (int): Number of attention heads.
        window_size (int): Local window size.
        mlp_ratio (float): Ratio of mlp hidden dim to embedding dim.
        qkv_bias (bool, optional): If True, add a learnable bias to query, key, value. Default: True
        qk_scale (float | None, optional): Override default qk scale of head_dim ** -0.5 if set.
        drop (float, optional): Dropout rate. Default: 0.0
        attn_drop (float, optional): Attention dropout rate. Default: 0.0
        drop_path (float | tuple[float], optional): Stochastic depth rate. Default: 0.0
        norm_layer (nn.Module, optional): Normalization layer. Default: nn.LayerNorm
        downsample (nn.Module | None, optional): Downsample layer at the end of the layer. Default: None
        use_checkpoint (bool): Whether to use checkpointing to save memory. Default: False.
    """

    def __init__(self, dim, input_resolution, depth, num_heads, window_size,
                 mlp_ratio=4., qkv_bias=True, qk_scale=None, drop=0., attn_drop=0.,
                 drop_path=0., norm_layer=nn.LayerNorm, downsample=None, use_checkpoint=False):

        super().__init__()
        self.dim = dim
        self.input_resolution = input_resolution
        self.depth = depth
        self.use_checkpoint = use_checkpoint

        # build blocks
        self.blocks = nn.ModuleList([
            SwinTransformerBlock(dim=dim, input_resolution=input_resolution,
                                 num_heads=num_heads, window_size=window_size,
                                 shift_size=0 if (i % 2 == 0) else window_size // 2,
                                 mlp_ratio=mlp_ratio,
                                 qkv_bias=qkv_bias, qk_scale=qk_scale,
                                 drop=drop, attn_drop=attn_drop,
                                 drop_path=drop_path[i] if isinstance(drop_path, list) else drop_path,
                                 norm_layer=norm_layer)
            for i in range(depth)])

        # patch merging layer
        if downsample is not None:
            self.downsample = downsample(input_resolution, dim=dim, norm_layer=norm_layer)
        else:
            self.downsample = None

    def forward(self, x):
        for blk in self.blocks:
            if self.use_checkpoint:
                x = checkpoint.checkpoint(blk, x)
            else:
                x = blk(x)
        if self.downsample is not None:
            x = self.downsample(x)
        return x

    def extra_repr(self) -> str:
        return f"dim={self.dim}, input_resolution={self.input_resolution}, depth={self.depth}"

    def flops(self):
        flops = 0
        for blk in self.blocks:
            flops += blk.flops()
        if self.downsample is not None:
            flops += self.downsample.flops()
        return flops


class PatchEmbed(nn.Module):
    r""" Image to Patch Embedding
    Args:
        img_size (int): Image size.  Default: 224.
        patch_size (int): Patch token size. Default: 4.
        in_chans (int): Number of input image channels. Default: 3.
        embed_dim (int): Number of linear projection output channels. Default: 96.
        norm_layer (nn.Module, optional): Normalization layer. Default: None
    """

    def __init__(self, img_size=224, patch_size=4, in_chans=3, embed_dim=96, norm_layer=None):
        super().__init__()
        img_size = to_2tuple(img_size)
        patch_size = to_2tuple(patch_size)
        patches_resolution = [img_size[0] // patch_size[0], img_size[1] // patch_size[1]]
        self.img_size = img_size
        self.patch_size = patch_size
        self.patches_resolution = patches_resolution
        self.num_patches = patches_resolution[0] * patches_resolution[1]

        self.in_chans = in_chans
        self.embed_dim = embed_dim

        self.proj = nn.Conv2d(in_chans, embed_dim, kernel_size=patch_size, stride=patch_size)
        if norm_layer is not None:
            self.norm = norm_layer(embed_dim)
        else:
            self.norm = None

    def forward(self, x):
        B, C, H, W = x.shape
        # FIXME look at relaxing size constraints
        assert H == self.img_size[0] and W == self.img_size[1], \
            f"Input image size ({H}*{W}) doesn't match model ({self.img_size[0]}*{self.img_size[1]})."
        x = self.proj(x).flatten(2).transpose(1, 2)  # B Ph*Pw C
        if self.norm is not None:
            x = self.norm(x)
        return x

    def flops(self):
        Ho, Wo = self.patches_resolution
        flops = Ho * Wo * self.embed_dim * self.in_chans * (self.patch_size[0] * self.patch_size[1])
        if self.norm is not None:
            flops += Ho * Wo * self.embed_dim
        return flops


class SwinTransformer(nn.Module):
    r""" Swin Transformer
        A PyTorch impl of : `Swin Transformer: Hierarchical Vision Transformer using Shifted Windows`  -
          https://arxiv.org/pdf/2103.14030
    Args:
        img_size (int | tuple(int)): Input image size. Default 224
        patch_size (int | tuple(int)): Patch size. Default: 4
        in_chans (int): Number of input image channels. Default: 3
        num_classes (int): Number of classes for classification head. Default: 1000
        embed_dim (int): Patch embedding dimension. Default: 96
        depths (tuple(int)): Depth of each Swin Transformer layer.
        num_heads (tuple(int)): Number of attention heads in different layers.
        window_size (int): Window size. Default: 7
        mlp_ratio (float): Ratio of mlp hidden dim to embedding dim. Default: 4
        qkv_bias (bool): If True, add a learnable bias to query, key, value. Default: True
        qk_scale (float): Override default qk scale of head_dim ** -0.5 if set. Default: None
        drop_rate (float): Dropout rate. Default: 0
        attn_drop_rate (float): Attention dropout rate. Default: 0
        drop_path_rate (float): Stochastic depth rate. Default: 0.1
        norm_layer (nn.Module): Normalization layer. Default: nn.LayerNorm.
        ape (bool): If True, add absolute position embedding to the patch embedding. Default: False
        patch_norm (bool): If True, add normalization after patch embedding. Default: True
        use_checkpoint (bool): Whether to use checkpointing to save memory. Default: False
    """

    def __init__(self, img_size=224, patch_size=4, in_chans=3, num_classes=1000,
                 embed_dim=96, depths=[2, 2, 6, 2], num_heads=[3, 6, 12, 24],
                 window_size=7, mlp_ratio=4., qkv_bias=True, qk_scale=None,
                 drop_rate=0., attn_drop_rate=0., drop_path_rate=0.1,
                 norm_layer=nn.LayerNorm, ape=False, patch_norm=True,
                 use_checkpoint=False, **kwargs):
        super().__init__()

        self.num_classes = num_classes
        self.num_layers = len(depths)
        self.embed_dim = embed_dim
        self.ape = ape
        self.patch_norm = patch_norm
        self.num_features = int(embed_dim * 2 ** (self.num_layers - 1))
        self.mlp_ratio = mlp_ratio

        # split image into non-overlapping patches
        self.patch_embed = PatchEmbed(
            img_size=img_size, patch_size=patch_size, in_chans=in_chans, embed_dim=embed_dim,
            norm_layer=norm_layer if self.patch_norm else None)
        num_patches = self.patch_embed.num_patches
        patches_resolution = self.patch_embed.patches_resolution
        self.patches_resolution = patches_resolution

        # absolute position embedding
        if self.ape:
            self.absolute_pos_embed = nn.Parameter(torch.zeros(1, num_patches, embed_dim))
            trunc_normal_(self.absolute_pos_embed, std=.02)

        self.pos_drop = nn.Dropout(p=drop_rate)

        # stochastic depth
        dpr = [x.item() for x in torch.linspace(0, drop_path_rate, sum(depths))]  # stochastic depth decay rule

        # build layers
        self.layers = nn.ModuleList()
        for i_layer in range(self.num_layers):
            layer = BasicLayer(dim=int(embed_dim * 2 ** i_layer),
                               input_resolution=(patches_resolution[0] // (2 ** i_layer),
                                                 patches_resolution[1] // (2 ** i_layer)),
                               depth=depths[i_layer],
                               num_heads=num_heads[i_layer],
                               window_size=window_size,
                               mlp_ratio=self.mlp_ratio,
                               qkv_bias=qkv_bias, qk_scale=qk_scale,
                               drop=drop_rate, attn_drop=attn_drop_rate,
                               drop_path=dpr[sum(depths[:i_layer]):sum(depths[:i_layer + 1])],
                               norm_layer=norm_layer,
                               downsample=PatchMerging if (i_layer < self.num_layers - 1) else None,
                               use_checkpoint=use_checkpoint)
            self.layers.append(layer)

        self.norm = norm_layer(self.num_features)
        self.avgpool = nn.AdaptiveAvgPool1d(1)
        self.head = nn.Linear(self.num_features, num_classes) if num_classes > 0 else nn.Identity()

        self.apply(self._init_weights)

    def _init_weights(self, m):
        if isinstance(m, nn.Linear):
            trunc_normal_(m.weight, std=.02)
            if isinstance(m, nn.Linear) and m.bias is not None:
                nn.init.constant_(m.bias, 0)
        elif isinstance(m, nn.LayerNorm):
            nn.init.constant_(m.bias, 0)
            nn.init.constant_(m.weight, 1.0)

    @torch.jit.ignore
    def no_weight_decay(self):
        return {'absolute_pos_embed'}

    @torch.jit.ignore
    def no_weight_decay_keywords(self):
        return {'relative_position_bias_table'}

    def forward_features(self, x):
        x = self.patch_embed(x)
        if self.ape:
            x = x + self.absolute_pos_embed
        x = self.pos_drop(x)

        for layer in self.layers:
            x = layer(x)

        x = self.norm(x)  # B L C
        x = self.avgpool(x.transpose(1, 2))  # B C 1
        x = torch.flatten(x, 1)
        return x

    def forward(self, x):
        x = self.forward_features(x)
        x = self.head(x)
        return x

    def flops(self):
        flops = 0
        flops += self.patch_embed.flops()
        for i, layer in enumerate(self.layers):
            flops += layer.flops()
        flops += self.num_features * self.patches_resolution[0] * self.patches_resolution[1] // (2 ** self.num_layers)
        flops += self.num_features * self.num_classes
        return flops
```
这就构成了接下来我要尝试操作和编辑的文本（哈哈，至于为什么用Swin Transformer代码，纯属随性而为，因为Swin Transformer太厉害了）。

接下来在当前路径`mydir`下，在Linux终端里输入下述命令：
``` bash
vim mytest.py
```
这样就用vim编辑器打开了`mytest.py`代码。此时，键入`:q`（冒号和q）和回车，就退出了vim编辑器打开的`mytest.py`代码，回到了当前目录`mydir`之下。

当我们在Linux终端里输入下述命令：
``` bash
vim mytest.py
```
之后，这就进入了**正常模式**之下。此时，如果按字母`x`键，会删除当前光标所在的字母。如果要撤销删除，需要按字母`u`键实现撤销。

我们再次在Linux终端里输入下述命令：
``` bash
vim mytest.py
```
之后我们按下字母`i`键，这样就进入了**插入模式**。插入模式可以实现文本的插入。比如，此时我们再按`x`，就能实现插入字母`x`的功能。要删除文本中的字母，只需要按下退格键`Backspace`即可。如果我们要保存对文本所做的修改，只需**回到正常模式**并按下`:wq`和回车退出即可。

从任何其他模式回到正常模式，只需按下键盘左上角的`Esc`即可。在正常模式之下，键入`i`进入插入模式，键入`R`进入替换模式，键入`v`进入可视（一般）模式，键入`V`进入可视（行）模式，`<C-v>`（`Ctrl-V`，有时也写作`^V`）进入可视（块）模式，`:`进入命令模式。
（注意：我目前暂时还不知道怎么进入可视块模式。我发现`Ctrl-V`似乎无法用在vscode远程连接的Linux终端里。这个问题我暂时还不知道怎么解决。）

在Vim的哲学思想里，使用鼠标是一件效率很低的事情。因此，Vim的一切操作都可以**只借助键盘**来完成。关于这一点，之后会慢慢体会到。

Vim还有一个很有用的模块是帮助模块。我们在Linux终端里键入`vim mytest.py`之后，再键入下述命令：
``` bash
:help :w
```
就可以查看`:w`的帮助。在帮助中可以看到，`:w`用于保存使用vim对文本的修改。在帮助模块中，再次键入`:q`，即可退出帮助模块，返回vim编辑器。

## vim命令行模式
打开vim之后，在正常模式下键入`:`进入命令行模式。**在键入`:`后，你的光标会立即跳到屏幕下方的命令行**。命令行模式有很多功能，包括打开，保存，关闭文件，以及退出vim：
- `:q`退出（关闭窗口）
- `:w`保存（写）
- `:wq`保存然后退出
- `:e {文件名}`打开要编辑的文件
- `:ls`显示打开的缓存
- `:help {标题}`打开帮助文档
  - `:help :w`打开`:w`命令的帮助文档
  - `:help w`打开`w`移动的帮助文档

关于Vim还有一点需要强调，就是：如果我操作失误，把文件搞乱了，这个时候，我无法退出，此时我强制kill了这个Linux终端，然后启动了一个新的终端。此时，在当前路径`mydir`之下运行`ls -al`命令，可以看到以`.`开头的隐藏文件（比如`.swp`文件）。这些隐藏文件是vim保存的临时文件，我必须用`rm .swp`删除这些隐藏文件，这样才能正常使用vim编辑其他文本文件。

## 在Vim中启用多个窗口
在vscode中，我们可以很方便地同时打开多个窗口，或者是同时编辑多个代码文件，在vim中，我们同样可以做到这件事。在正常模式下键入`:sp`，就可以打开一个和当前文本完全一样的窗口。注意：此时此刻，如果我们在插入模式之下对这个文本文件插入内容，则在两个窗口中会显示同步的插入。这种方法非常适合于同时看一个很长的代码的上面部分和下面部分的时候。

## 在vim中创建多标签页
当我们进入vim之后，我们可以在`:`命令行模式之下直接在当前路径下创建新的文本文件。比如，我们首先在Linux终端里运行`vim mytest.py`命令进入vim，然后在vim里运行下述命令：
``` bash
:tabnew Tony.py
```
这就直接在当前路径`mydir`之下创建了一个空白脚本`Tony.py`。此时，使用`i`进入插入模式，就可以键入代码了。比如，我们键入下述代码：
``` python
import torch

print('torch.__version__: ',torch.__version__)
print('torch.version.cuda: ',torch.version.cuda)
```
运行下述代码，结果为：
```
torch.__version__:  1.10.1
torch.version.cuda:  11.1
```
这就是我的PyTorch版本和PyTorch对应的cuda版本。这样，我们就学会了怎样在vim中直接在当前路径下创建新的文本文件并编辑文本文件。

当我们使用`:tabnew Tony.py`命令创建了新的选项卡`Tony.py`之后，如果我们想要在不同的选项卡之间移动，我们可以在vim中使用`:tabp`命令。此时，我们如果使用`:wq`或`:q`，只能关闭当前的标签页。

我们还可以重新打开多个标签页。比如，在使用`vim mytest.py`打开当前的`mytest.py`文件之后，在`vim`中键入`:tabedit Tony.py`，就可以重新打开`Tony.py`文件进行编辑（注意：可以在vim的命令模式中使用`Tab`键自动补全`Tony.py`的文件名）。

如果我们依次键入下述三个命令：
``` bash
vim mytest.py
:tabedit Tony.py
:tabedit apple.md
```
这样就同时打开了三个vim标签页。此时，键入`:qa`命令，就能实现同时关闭这三个标签页。

## 在vim中移动光标
在vim的正常模式之下，可以使用`h`、`j`、`k`、`l`分别实现向左、下、上、右移动。再次强调，前提是：必须在vim的正常模式之下，才能使用`h`、`j`、`k`、`l`分别实现向左、下、上、右移动。

在vim的正常模式之下移动光标的命令总结如下：
- 基本移动：`hjkl`（左，下，上，右）
- 词：`w`（下一个词），`b`（词初），`e`（词尾）
- 行：`0`（行初），`^`（第一个非空格字符），`$`（行尾）
- 屏幕：`H`（屏幕首行），`M`（屏幕中间），`L`（屏幕底部）
- 翻页：`Ctrl-u`（上翻），`Ctrl-d`（下翻）
- 文件：`gg`（文件头），`G`（文件尾）
- 行数：`:{行数}<CR>`或者`{行数}G` （{行数}为行数）
- 杂项：`%`（找到配对，比如括号或者 /* */ 之类的注释对）
- 查找：`f{字符}`，`t{字符}`，`F{字符}`，`T{字符}`
  - 向后查找/到/向前/向后 在本行的{字符}**（注意：我测试之后发现，这些查找只能在目前光标所在的行向前或向后查找定位）**
  - `,` / `;` 用于导航匹配
- 搜索：`/{正则表达式}`，`n`/`N`用于导航匹配

在插入模式中，如果插入了某些字符之后想要撤销，则只需要按`Esc`键回到正常模式之后，再多次按`u`，就可以撤销刚才的多次输入了。如果想要恢复刚才的工作，只需多次按下`Ctrl + R`或者按下`Ctrl`之后按住`R`，就可以依次恢复被撤销的工作。


## 使用vim来编辑文本
所有过去需要用鼠标做的事，现在都可以用键盘来做，意即：采用编辑命令和移动命令的组合来完成。
这就是Vim的界面开始看起来像一个程序语言的时候。Vim的编辑命令也被称为 “动词”，因为动词可以施动于名词。在vim的正常模式之下，可以使用下述的常用编辑命令：
- `i` 进入插入模式 
    - 但是对于操纵/编辑文本，不单想用退格键完成
- `O` / `o` 在之上/之下插入行
- `d{移动命令}` 删除 {移动命令}
    - 例如：`dw` 删除词, `d$` 删除到行尾, `d0` 删除到行头。
- `c{移动命令}` 改变 {移动命令}
    - 例如， `cw` 改变词
    - 比如 `d{移动命令}` 再 `i`
- `x` 删除字符（等同于 `dl`）
- `s` 替换字符（等同于 `xi`）
- 可视化模式 + 操作
    - 选中文字, `d` 删除 或者 `c` 改变
- `u` 撤销, `<C-r>` 重做
- `y` 复制 / "yank" （其他一些命令比如 `d` 也会复制）
- `p` 粘贴
- 更多值得学习的: 比如 `~` 改变字符的大小写

比如，我们在正常模式之下：
- 依次键入`dw`这两个字母就可以实现删除词的功能；
- 依次键入`d$`这两个字符就可以实现删除到行尾的功能；
- 依次键入`d0`就可以实现删除到行头的功能；
- 依次键入`de`可以实现删除到当前单词的末尾的功能。
- 依次键入`dd`可以实现删除当前行的功能

再比如，我们在正常模式之下：
- 依次键入`ce`可以实现删除到当前单词的末尾，同时进入插入模式，开始在当前光标位置处插入字符的功能。
- 依次键入`cc`可以实现删除当前行，同时进入插入模式，开始编辑当前行的功能

在正常模式之下，删除功能`d`和修改功能`c`具有类比性，这两者的用法是类似的。

还有，我们在正常模式之下：
- 依次键入`r{某个字母}`可以实现把当前光标处的字母提换成`r`之后键入的那个字母的功能

## 在vim中使用复制和粘贴
在vim的正常模式之下：
- 依次键入`yy`可以实现复制整行的功能，然后再按下`p`就可以在当前光标所在的行的下一行粘贴我刚刚复制的那一行内容了。此时，可以多次把光标移动到不同的位置，然后按下`p`，这样可以多次粘贴同一行的内容到不同的多行。
- 依次键入`yw`可以实现复制当前这个单词的功能。
- 将光标移动到当前感兴趣的点，然后按下`v`进入可视（一般）模式，然后就可以使用`hjkl`来移动光标选择多行了。选中多行以后，键入`d`可以实现删除这多行，键入`y`可以实现复制这多行。然后，定位到指定地点，就可以键入`p`来粘贴刚刚复制的多行内容了。**注意：在复制了某些内容之后，可以先进入插入模式进行一些其他的编辑，然后再回到正常模式中来粘贴内容。并不一定非得复制之后立马就要粘贴所复制的内容。**
- 将光标位置定位到当前的一个单词的开头，然后依次键入`vw`选中当前的单词，再键入`~`，就可以实现转换大小写字母的功能。