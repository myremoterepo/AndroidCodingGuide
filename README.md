- [x] 我不是老司机，我会认真看。

# AndroidCodingGuide

## 命名
想象别人看到这个名字的反应，用名字表达出意义。
### 信息装入名字
* 使用专业的单词

        send -> deliver, dispatch, announce, distribute, route
        find -> search, extract, locate, recover
        start -> lanuch, create, begin, open
        make -> create,set up, build, generate, compose, add, new
* 避免空泛的名字
* 使用具体的名字更细致的描述对象
* 给变量名带上重要细节

        password -> plaintext_password
        comment -> unescaped_comment
        html -> html_utf8
        data -> data_urlenc
* 为作用域大的名字采用更长的名字
* 有目的的使用大小写，下划线等
* 不要使名字产生误解

### 常用名字习惯
* 使用is和has这样的词来明确表示它是布尔值，避免使用反义的词
* 用min和max来表示极限
* 用first和last来表示包含的范围
* 用begin和end来表示包含/排除范围
* 一致的方法名字，openXX/closeXX。

### 包名
采用反写域名命名规则，com.browser2345

### 类名
采用驼峰命名法，尽量避免缩写，除非该缩写是众所周知的

### 变量名
* 常量：大写字母，单词间用下划线连接。如MAX_OPEN_FILES
* 静态变量：驼峰，以“s”开头。如sMaxOpenFiles
* 全局非静态变量:驼峰，以“m”开头。如mMaxOpenFiles

### 方法名
* 同样的上下文里，越短的名字越容易读，容易理解，容易定位
* 在表示清楚含义的同时，尽量短

## 写方法
* 很长的方法让整个类变得很长，影响阅读、理解、定位
* 一次只做一件事，职责单一化
* 方法的参数列表要短，参数列表变长会使方法变得复杂
* 可考虑将长的参数列表封装成一个类
* 做同样事情的方法，在数据或者操作上有差异，可使用泛型或者解释器等减少代码
* 想获取一个参数，只提供一种途径/方法
* 瞻前顾后，考虑兼容，变化，各种如果

## 注释
变量，常量，方法， 类等都需要

### 不对的想法
* 懒得写注释
* 先把所有的内容写完，回头再写注释
* 不负责任的写注释

### 好的注释
* 对代码的进一步阐述，形象而不抽象
* 让人明白你的思路/想法
* 为什么这么写，代码的缺陷或可能引起的风险，防止被勿用
* 不要将注释放到代码的同一行
* 遵循一定的格式 //&/* */

### 不需要注释的地方
* 可以从代码迅速推断出事实
* 粉饰烂代码的注释-将代码改好

## 代码风格
### 漂亮的格式
* 习惯于使用Android Studio快捷方式的开发，要配置代码格式，与team统一
* 空格使用一致
* 多个代码块做相似的事情，让他们有相同的剪影
* 让代码按列对齐，方便浏览
* 一段代码中提到ABC后，在后面使用时始终保持ABC的顺序
* 空行将一段代码分组，形成代码块。声明中以widiget分组，方法中操作对象分组等

### Android Studio编辑界面好用的设置
* 显示行号
* 驼峰选择
* 命名前缀（s， m）
* 代码配色
工欲善其事，必先利其器。参考http://www.jianshu.com/p/4243f3b52644

## 临时变量
* 减少临时变量的声明

        if (item != null) {
            /*int downloadId = item.mId;
            intent.putExtra("downloadId", downloadId);*/
            intent.putExtra("downloadId", item.mId);
        }
* 传递会使用的变量
* 传递的对象只包含会使用的成员

## 重用代码
* 给一些有相同代码的类抽接口
* 将重复使用的方法抽成工具类/模板类
* 当然，用的时候先去这些类去搜索。有则用，无则加

## 作用域问题
* 声明的变量该私有的不要公开
* 不该暴露的方法不要公开

## 控制流
* if..else..
* 说一下"尤达表示法"，为了防止if(obj == NULL)被写成if(obj = NULL)，在Java有编译器检测，不会发生
* try..catch..
* switch..case..default..
* 代码流程中断后的操作

### 三目运算符
* 对代码上下文了解的情况下，使用三目运算符可节省代码行数。替换简单的if..else..
* 不要使理解变复杂
* 使用不当时，Android Studio编译器会有提示，根据提示和上下文修改

### 避免深嵌套
循环和逻辑控制的嵌套会导致代码层级越来越深，是代码可读性差的罪魁祸首

## Little Thing

### 复杂的逻辑条件
DownloadNotification.java:

    for(..){
        if (item.state == 2) {
            if (!item.stateChange || (System.currentTimeMillis() - item.lastMod >= 3 * 1000 * 60)) {
                continue;
            }
        } else if (item.state == 0 && !item.stateChange) {
            continue;
        }
        ...
    }

合并判断语句：

    if ((item.state == 2
            && (!item.stateChange || (System.currentTimeMillis() - item.lastMod >= 3 * 1000 * 60)))
            || (item.state == 0 && !item.stateChange)) continue;
更好的：

    // 下载完成状态
    if (item.state == 2
            && (!item.stateChange || (System.currentTimeMillis() - item.lastMod >= 3 * 1000 * 60))) continue;
    // 下载暂停状态
    if (item.state == 0
            && !item.stateChange) continue;
虽然有两个if语句，行数增加，单更容易理解。

### 数据传递
HistoryAdapterNew.java:

    public HistoryAdapterNew(ListView listView, Context ctx, ArrayList<String> mGroupDatas,
                             ArrayList<List<HistoryItemEntity>> mDatas) {
        this.mListView = listView;
        this.mContext = ctx;

        /*this.mGroupDatas = mGroupDatas;
        this.mDatas = mDatas;*/

        ArrayList<List<HistoryItemEntity>> tempDatas = new ArrayList<List<HistoryItemEntity>>();
        ArrayList<String> tempGroupDatas = new ArrayList<String>();
        tempGroupDatas.addAll(mGroupDatas);
        tempDatas.addAll(mDatas);
        this.mGroupDatas = tempGroupDatas;
        this.mDatas = tempDatas;

        mBookmarkAniOffset = (int) ctx.getResources().getDimension(R.dimen.download_list_offset);
    }

### 字符串操作
SuggestionsAdapter.java:

    private void makePattern(String word) {
        try {
            pattern = Pattern.compile(word, Pattern.UNICODE_CASE | Pattern.CASE_INSENSITIVE);
        } catch (Exception e) {
            pattern = Pattern.compile("");
            e.printStackTrace();
        }
    }

### 日志
Controller.java:

    public void onSuccess(QQAppReplaceBean qqAppBean, Call call, Response response) {
        super.onSuccess(qqAppBean, call, response);
        boolean downloadError = false;
        Log.i("QQAPP", "replaceAppRequest " + qqAppBean.head.toString())
        if (qqAppBean != null && qqAppBean.body != null && qqAppBean.body.appInfo != null
                      && !TextUtils.isEmpty(qqAppBean.body.appInfo.apkUrl)) {
            ...
        }
        avoidContinuousClick(downloadError);
    }
无用或测试的日志及时删除。

## 布局文件
* id有重复
* 项目里的id最好做到唯一，在copy布局文件以后，给id加上前缀，标明其归属或者功能。

## Dead Code
我们不怕删代码，我们有Git管理代码。

## 改动代码
* 最好限制在当前的方法/类, 不要对其他的产生影响。
* 代码

## 写思考的同时要多想一些
* 时间成本
* 维护成本
* 内存影响、耗电影响、主线程影响
* 对代码上下文的影响

### 一个栗子，新闻详情页右滑退出功能时，在退出后发现Fragment并没有销毁。

## 项目结构
* 现在是类MVC
* 应用主流架构：MVP、MVVM
* 先研究，在独立的小模块可以先使用。


## 欢迎异议，给我指导！
