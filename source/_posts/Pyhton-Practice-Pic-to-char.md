---
title: 'Pyhton-Practice:Pic-to-char'
date: 2017-12-21 18:11:03
tags:
category: Python
---
- title: Convert Pic To Chargraphic
- principle: every pixel has a RGB value, one char can replaces one RGB value or some RGB value, then a pic consisted of pixels can be converted to a document with char
- code:<!-- more -->
```
from PIL import Image
import argparse

# 添加命令行参数
parser = argparse.ArgumentParser()
parser.add_argument('file')
parser.add_argument('-o')
parser.add_argument('--width', default=80, type=int)
parser.add_argument('--height', default=80, type=int)

args = parser.parse_args()

IMG = args.file
OUTPUT = args.o
WIDTH = args.width
HEIGHT = args.height

ascii_char = list("0 ")
length = len(ascii_char)

# convert pixel value to char
def get_char(r,g,b,alpha=256):
    if alpha == 0:
        return ' '
    gray = 0.2*r + 0.7*g + 0.1*b
    unit = (256.0 + 1)/length
    return ascii_char[int(gray/unit)]

if __name__ == '__main__':
    im = Image.open(IMG)
    im = im.resize((WIDTH,HEIGHT), Image.NEAREST)

    txt = ''

    for i in range(HEIGHT):
        for j in range(WIDTH):
            txt += get_char(*im.getpixel((j,i)))
        txt += '\n'
# output chars to document
    if OUTPUT:
        with open(OUTPUT, 'w') as f:
            f.write(txt)
    else:
        with open('./output.txt', 'w') as f:
            f.write(txt)
```
