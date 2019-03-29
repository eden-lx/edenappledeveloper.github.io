---
title: iOS关于富文本字符串
date: 2019-03-29 09:37:22
tags:
---


首先要创建一个带有属性的字符串NSMutableAttributedString

    NSMutableAttributedString *attrString = [[NSMutableAttributedString alloc] initWithString:@"这是一个富文本字符串"]; 
    /* 其他几种创建方法
    - (instancetype)initWithString:(NSString *)str;
    - (instancetype)initWithString:(NSString *)str attributes:(nullable NSDictionary<NSString *, id> *)attrs;
    - (instancetype)initWithAttributedString:(NSAttributedString *)attrStr;
    */


<!-- more -->

如何设置这个字符串的属性（以下两个方法：）

    - (void)addAttribute:(NSString *)name value:(id)value range:(NSRange)range; // 每次设置一个属性和它对应的值
    - (void)addAttributes:(NSDictionary<NSString *, id> *)attrs range:(NSRange)range; // 一次可以设置多个属性 属性和属性值以字典键值对形式进行设置
    // range参数是设置要设置属性的字符串范围


常用的属性都有哪些

- 字体


        [attrStr addAttribute:NSFontAttributeName value:[UIFont systemFontOfSize:30.0f] range:NSMakeRange(4, 3)];

- 颜色


        [attrStr addAttribute:NSForegroundColorAttributeName value:[UIColor redColor] range:NSMakeRange(4, 3)];


- 背景颜色


        [attrStr addAttribute:NSBackgroundColorAttributeName value:[UIColor redColor] range:NSMakeRange(4, 3)];


- 空心字


        [attrStr addAttribute:NSStrokeColorAttributeName
            value:[UIColor redColor]
            range:NSMakeRange(4, 3)];  // 设置描边颜色 要和NSStrokeWidthAttributeName设置描边宽度一起使用
        [attrStr addAttribute:NSStrokeWidthAttributeName
            value:@1.5
            range:NSMakeRange(4, 3)]; 


- 间距

        [attrStr addAttribute:NSKernAttributeName
                        value:@10                    // NSNumber
                        range:NSMakeRange(4, 3)];


- 倾斜


        [attrStr addAttribute:NSObliquenessAttributeName
                        value:@(0.5f)          // 正值向右倾斜 负值向左倾斜
                        range:NSMakeRange(4, 3)];


- 拉伸、压缩


        [attrStr addAttribute:NSExpansionAttributeName
                        value:@(0.5f)      // 正值横向拉伸 负值横向压缩
                        range:NSMakeRange(4, 3)];


- 连体


        NSMutableAttributedString * attrStr = [[NSMutableAttributedString alloc] initWithString:@"'flush' is a string！"];
        [attrStr addAttribute:NSLigatureAttributeName
                        value:@1
                        range:NSMakeRange(1, 5)];
        [attrStr addAttribute:NSFontAttributeName
                        value:[UIFont fontWithName:@"futura" size:30]
                        range:NSMakeRange(1, 5)];


- 基线偏移量


        [attrStr addAttribute:NSBaselineOffsetAttributeName
                        value:@(10)   // 正值上偏 负值下偏
                        range:NSMakeRange(4, 3)];


- 阴影


        NSShadow *shadow = [[NSShadow alloc] init];  // NSShadow只有3个属性：阴影颜色，模糊半径和偏移
        shadow.shadowOffset     = CGSizeMake(3, 3);   // 阴影偏移（X方向偏移和Y方向偏移）
        shadow.shadowBlurRadius = 1.5;                // 模糊半径
        shadow.shadowColor      = [UIColor redColor]; // 阴影颜色

        [attrStr addAttribute:NSShadowAttributeName
        value:shadow
        range:NSMakeRange(4, 3)];


- 特殊效果


        [attrStr addAttribute:NSTextEffectAttributeName
        value:NSTextEffectLetterpressStyle  // NSString类型 目前只有NSTextEffectLetterpressStyle(凸版印刷效果)可用
        range:NSMakeRange(4, 3)];


- 书写方向


        [attrStr addAttribute:NSWritingDirectionAttributeName
                        value:@[@(NSWritingDirectionRightToLeft | NSWritingDirectionOverride)]
                        range:NSMakeRange(0, attrStr.length)];


        //NSWritingDirectionAttributeName 设置文字书写方向，取值为以下组合
        // iOS9.0以前
        //@[@(NSWritingDirectionLeftToRight | NSTextWritingDirectionEmbedding)]
        //@[@(NSWritingDirectionLeftToRight | NSTextWritingDirectionOverride)]
        //@[@(NSWritingDirectionRightToLeft | NSTextWritingDirectionEmbedding)]
        //@[@(NSWritingDirectionRightToLeft | NSTextWritingDirectionOverride)]
        // iOS9.0以后
        //@[@(NSWritingDirectionLeftToRight | NSWritingDirectionEmbedding)]
        //@[@(NSWritingDirectionLeftToRight | NSWritingDirectionOverride)]
        //@[@(NSWritingDirectionRightToLeft | NSWritingDirectionEmbedding)]
        //@[@(NSWritingDirectionRightToLeft | NSWritingDirectionOverride)]
        // NSWritingDirectionOverride 和 NSWritingDirectionEmbedding 是指定Unicode双向定义的格式控制算法（具体的没太搞清楚）


- 横、竖排版


        [attrStr addAttribute:NSVerticalGlyphFormAttributeName
                        value:@0   // 0横向排版 1竖向排版 iOS中除了0以外都未定义，所以都为横向排版
                        range:NSMakeRange(0, attrStr.length)];


- 下划线


        [attrStr addAttribute:NSUnderlineStyleAttributeName
                        value:@(NSUnderlineStyleSingle)
                        range:NSMakeRange(4, 3)];


        [attrStr addAttribute:NSUnderlineColorAttributeName
                        value:[UIColor redColor]
                        range:NSMakeRange(4, 3)];



- 删除线


        [attrStr addAttribute:NSStrikethroughStyleAttributeName
                        value:@(NSUnderlinePatternSolid | NSUnderlineStyleSingle)
                        range:NSMakeRange(4, 3)];


        [attrStr addAttribute:NSStrikethroughColorAttributeName
                        value:[UIColor redColor]
                        range:NSMakeRange(4, 3)];



- 网址


        NSMutableAttributedString * attrStr = [[NSMutableAttributedString alloc] initWithString:@"这是一个网址http://www.jianshu.com/users/37f2920f6848字符串！"];
        [attrStr addAttribute:NSLinkAttributeName
        value:@"http://www.jianshu.com/users/37f2920f6848"
        range:NSMakeRange(6, attrStr.length -6 -4)];
        // label显示出来的连接是点击不了的 textView的是可以的 有响应的回调函数shouldInteractWithURL



- 图文混排


        NSMutableAttributedString * attrStr = [[NSMutableAttributedString alloc] initWithString:@"这是一个富文本字符串！"];
        // 创建一个文字附件对象
        NSTextAttachment *textAttachment = [[NSTextAttachment alloc] init];
        textAttachment.image = [UIImage imageNamed:@"11.png"];  //设置图片源
        textAttachment.bounds = CGRectMake(0, -6, 30, 30);  //设置图片位置和大小
        // 将文字附件转换成属性字符串
        NSAttributedString *attachmentAttrStr = [NSAttributedString attributedStringWithAttachment:textAttachment];
        // 将转换成属性字符串插入到目标字符串
        [attrStr insertAttributedString:attachmentAttrStr atIndex:8];



- 段落


        NSMutableParagraphStyle *paragraphStyle = [[NSMutableParagraphStyle alloc] init];
        // 行间距
        paragraphStyle.lineSpacing = 15.f;
        // 段落间距
        paragraphStyle.paragraphSpacing = 30.f;
        // 段落缩进像素
        paragraphStyle.firstLineHeadIndent = 40.f;
        // 整体缩进像素
        paragraphStyle.headIndent = 15.f;
        // 对齐方式
        paragraphStyle.alignment = NSTextAlignmentLeft;
        // 其他属性请自行查阅NSMutableParagraphStyle头文件
        
        NSMutableAttributedString * attrStr = [[NSMutableAttributedString alloc] initWithString:@"这是一个富文本字符串。\n这是一个富文本字符串？\n这是一个富文本字符串！"];
        [attrStr addAttribute:NSParagraphStyleAttributeName
                        value:paragraphStyle
                        range:NSMakeRange(0, attrStr.length)];




