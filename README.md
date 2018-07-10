jieba
========
jieba(结巴)中文分词：中文分词组件


特点
========
* jieba的三种分词模式：
    * 精确模式，试图将句子最精确地切开，适合文本分析；
    * 全模式，把句子中所有的可以成词的词语都扫描出来, 速度非常快，但是不能解决歧义；
    * 搜索引擎模式，在精确模式的基础上，对长词再次切分，提高召回率，适合用于搜索引擎分词。

* 支持繁体分词
* 支持自定义词典
* MIT 授权协议

算法
========
* 基于前缀词典实现高效的词图扫描，生成句子中汉字所有可能成词情况所构成的有向无环图 (DAG)
* 采用了动态规划查找最大概率路径, 找出基于词频的最大切分组合
* 对于未登录词，采用了基于汉字成词能力的 HMM 模型，使用了 Viterbi 算法

主要功能
=======
一. 分词
--------
jieba中的方法
* `jieba.cut`(str=?,cut_all=?,HMM=?)
* `jieba.cut_for_search(str=?,cut_all=?,HMM=?)` 该方法适合用于搜索引擎构建倒排索引的分词，粒度比较细
    jieba.cut_for_search和jieba.cut方法返回的是一个generator，可以用for循环迭代
* `jieba.lcut_for_search`和`jieba.lcut`是将字符串切分后，返回一个列表
* `jieba.Tokenizer(dictionary=DEFAULT_DICT)` 新建自定义分词器，可用于同时使用不同词典。`jieba.dt` 为默认分词器，所有全局分词相关函数都是该分词器的映射。
'''
例子：
`import jieba`
`cut_all` 参数用来控制是否采用全模式,默认是精确模式
`seg_list = jieba.cut("欢迎来到厦门旅游",cut_all=True)`
`print("切分后："+"/".join(seg_list))` 全模式，把所有可能成词的都分出来，可能某些字重复组成词

`seg_list = jieba.cut("欢迎来到厦门旅游", cut_all=False)`
`print("切分后: " + "/ ".join(seg_list))`  精确模式，把所有可能成词的都分出来，字不会重复组成词

`seg_list = jieba.cut("他来到了网易杭研大厦")` 默认是精确模式
`print("切分后: " + "/ ".join(seg_list))` 新词识别，杭研不在词典中也能自动识别

#搜索引擎模式，在'精确模式'的基础上，对长词再次切分，提高召回率，适合用于搜索引擎分词
`seg_list = jieba.cut_for_search("小明硕士毕业于中国科学院计算所，后在日本京都大学深造")` 搜索引擎模式
`print(", ".join(seg_list))`


二. 添加自定义词典
----------------
    1,添加自定义词典，开发者可以添加自定义词典，以便包含jieba词库里面没有的词典，虽然jieba
      有新词识别能力，但是添加自定义词典能够提高正确率
    2,用法:`jieba.load_userdict(file_name)`
    3,词典格式和 dict.txt 一样，一个词占一行；每一行分三部分：词语、词频（可省略）、词性（可省略），
      用空格隔开，顺序不可颠倒。`file_name` 若为路径或二进制方式打开的文件，则文件必须为 UTF-8 编码。
    4,词频省略时使用自动计算的能保证分出该词的词频
    5,使用 add_word(word, freq=None, tag=None) 和 del_word(word) 可在程序中动态修改词典。
    6,使用 suggest_freq(segment, tune=True) 可调节单个词语的词频，使其能（或不能）被分出来。
    7,注意：自动计算的词频(也就是自定义词典中省略磁盘词频的)在使用 HMM 新词发现功能时可能无效。

例子
----------------
    from __future__ import print_function, unicode_literals
    import sys
    #sys.path.append("../")
    import jieba
    jieba.load_userdict("user_dict1.txt") 加载词典
    import jieba.posseg as pseg

    jieba.add_word('石墨烯')
    jieba.add_word('凱特琳')
    jieba.del_word('自定义词')
    #测试字符串，元组
    test_sent = (
    "李小福是创新办主任也是云计算方面的专家; 什么是八一双鹿\n"
    "例如我输入一个带“韩玉赏鉴”的标题，在自定义词库中也增加了此词为N类\n"
    "「台中」正確應該不會被切開。mac上可分出「石墨烯」；此時又可以分出來凱特琳了。"
    )
    '''
    jieba.cut,posseg.cut的区别是posseg.cut分词后带了一个词性
    '''
    words = jieba.cut(test_sent)
    print('/'.join(words))

    print("="*40)
    #切分字符串
    result = pseg.cut(test_sent)
    print("-----------")
    for w in result:
        print(w.word, "/", w.flag, ", ", end=' ')

    print("\n" + "="*40)

    terms = jieba.cut('easy_install is great')
    print('/'.join(terms))
    terms = jieba.cut('python 的正则表达式是好用的')
    print('/'.join(terms))

    print("="*40)
    # test frequency tune
    testlist = [
    ('今天天气不错', ('今天', '天气')),
    ('如果放到post中将出错。', ('中', '将')),
    ('我们中出了一个叛徒', ('中', '出')),
    ]

    for sent, seg in testlist:
        print('/'.join(jieba.cut(sent, HMM=False)))
        word = ''.join(seg)
        print('%s Before: %s, After: %s' % (word, jieba.get_FREQ(word), jieba.suggest_freq(seg, True)))
        print('/'.join(jieba.cut(sent, HMM=False)))
        print("-"*40)

更改分词器（默认为 `jieba.dt`）的 `tmp_dir` 和 `cache_file` 属性，可分别指定缓存文件所在的文件夹及其文件名，用于受限的文件系统。

例子
----------------

    import jieba
    #不适用HMM模式
    print('/'.join(jieba.cut('如果放到post中将出错。', HMM=False)))
    #可调节单个词语的词频，使其能（或不能）被分出来，这里调整‘中’和‘将’的词频，使‘中’和‘将’
    #能单独被分开
    jieba.suggest_freq(('中', '将'), True)
    print('/'.join(jieba.cut('如果放到post中将出错。', HMM=False)))
    print('/'.join(jieba.cut('「台中」正确应该不会被切开', HMM=False)))
    #这里调整‘台中’的词频，使‘台中’不能单独被分开
    jieba.suggest_freq('台中', True)
    print('/'.join(jieba.cut('「台中」正确应该不会被切开', HMM=False)))
    

### 调整词典


三. 关键词提取
-------------
### 基于 TF-IDF 算法的关键词抽取
使用方法
-------------
    1,jieba.analyse.extract_tags(sentense,topK=20,withWeight=False, allowPOS=())
      jieba.analyse.extract_tags(sentence, topK=20, withWeight=False, allowPOS=())
          sentence 为待提取的文本
          topK 为返回几个 TF/IDF 权重最大的关键词，默认值为 20
          withWeight 为是否一并返回关键词权重值，默认值为 False
          allowPOS 仅包括指定词性的词，默认值为空，即不筛选
          jieba.analyse.TFIDF(idf_path=None) 新建 TFIDF 实例，idf_path 为 IDF 频率文件
    2,关键词提取所使用逆向文件频率（IDF）文本语料库可以切换成自定义语料库的路径
    3,用法： jieba.analyse.set_idf_path(file_name) # file_name为自定义语料库的路径
    4,关键词提取所使用停止词（Stop Words）文本语料库可以切换成自定义语料库的路径
    5,用法： jieba.analyse.set_stop_words(file_name) # file_name为自定义语料库的路径
    6,停用词是指一些中文中出现的语气词或连接词，这些词如果不进行踢出，会影响核心词与分类的
      明确关系。比如“的”，“之”，“与”，“和”等，也可以视情况增加适合本分类场景的停用词。
      中文停用词表涵盖了1598个停用词。可以从github上获取。

代码示例 （关键词提取）
-----------------------------
    import jieba.analyse
    s = "此外，公司拟对全资子公司吉林欧亚置业有限公司增资4.3亿元，增资后,吉林欧亚置业注册资本由7000万元增加\
    到5亿元。吉林欧亚置业主要经营范围为房地产开发及百货零售等业务。目前在建吉林欧亚城市商业综合体项目。\
    2013年，实现营业收入0万元，实现净利润-139.13万元。"
    #sentence：待提取文本    topK：数量    withWeight：是否一并返回关键词权重值
    #allowPOS：仅包括指定词性的词，默认值为空，即不筛选
    # 关键词提取所使用逆向文件频率（IDF）文本语料库可以切换成自定义语料库的路径
    #这里使用的是默认文本语料库
    for x, w in jieba.analyse.extract_tags(s, withWeight=True):   
        print('%s %s' % (x, w))
    print("="*40)
    # 基于 TextRank 算法的关键词抽取
    #直接使用，接口相同，注意默认过滤词性。限定的词性
    a = jieba.analyse.textrank(s, topK=20, withWeight=False, allowPOS=('ns', 'n', 'vn', 'v'))

    for x, w in jieba.analyse.textrank(s, withWeight=True):
        print('%s %s' % (x, w))

### 基于 TextRank 算法的关键词抽取(代码实例在上面)

* jieba.analyse.textrank(sentence, topK=20, withWeight=False, allowPOS=('ns', 'n', 'vn', 'v')) 直接使用，接口相同，注意默认过滤词性。
* jieba.analyse.TextRank() 新建自定义 TextRank 实例

四. 词性标注
-----------
    1,,jieba.Tokenizer(dictionary=DEFAULT_DICT) 新建自定义分词器，可用于同时使用不同词典。
      jieba.dt 为默认分词器，所有全局分词相关函数都是该分词器的映射
    2,更改分词器（默认为 jieba.dt）的 tmp_dir 和 cache_file 属性，可分别指定缓存文件所在的文件
      夹及其文件名，用于受限的文件系统。

例子：
--------------------
    import jieba.posseg as pseg
    words = pseg.cut("我爱北京天安门")
    for word, flag in words:
        print('%s %s' % (word, flag))


五. 并行分词
-----------
    1,原理：将目标文本按行分隔后，把各行文本分配到多个 Python 进程并行分词，然后归并结果，从而获得分
      词速度的可观提升
    2,基于 python 自带的 multiprocessing 模块，目前暂不支持 Windows
      用法:jieba.enable_parallel()
           jieba.enable_parallel(4) # 开启并行分词模式，参数为并行进程数
           jieba.disable_parallel() # 关闭并行分词模式
    3,Tokenize：返回词语在原文的起止位置
      注意，输入参数只接受 unicode
      默认模式

例子：
--------------
    #例子,tokenize返回一个generator生成器，包含多个字符串，每个字符串包含词语起止位置
    result = jieba.tokenize(u'永和服装饰品有限公司')
    print(type(result))
    for tk in result:
        print("wors %s\t\t start:%d\t\t end:%d" %(tk[0],tk[1],tk[2]))

    #搜索模式
    result = jieba.tokenize(u'永和服装饰品有限公司', mode='search')
    for tk in result:
        print("word %s\t\t start: %d \t\t end:%d" % (tk[0],tk[1],tk[2]))

    # ChineseAnalyzer for Whoosh 搜索引擎

