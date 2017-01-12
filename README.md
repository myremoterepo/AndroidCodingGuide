- [x] 我不是老司机，我会认真看。

# AndroidCodingGuide

## 注释
* 对代码的进一步阐述，形象而不抽象。
* 避免不必要的注释。
* 机器不认识注释，人才认识。

## 方法名
* 同样的上下文里，越短的名字越容易读，容易理解，容易定位。
* 在表示清楚含义的。
* 方法的参数列表要短，越长越复杂。（考虑传一个对象）

## 重用代码

## 复杂的逻辑条件

## 方法暴露/公开

* 做同样事情的方法，在数据或者操作上有差异，可使用泛型或者解释器等减少代码。

## 冗长的类或方法
* 冗长的类可能会用长的方法。
* 影响阅读、理解、定位。
* 一次只做一件事，职责单一化。
## 命名
* 名字可以表达信息。
* 别人要能理解。
* 一致的方法名字，openXX/closeXX。

## dead code

## 瞻前顾后
* 考虑兼容，变化，各种如果。

## 唯一
* 想获取一个参数，只提供一种途径/方法。
* 布局文件的id定义，copy布局文件后改一下，加上前缀，标明归属或功能。
## 临时变量
* 减少变量
* 传递对象时， 对象只包含要使用的参数。
## 相似的类抽接口
## 作用域
* 变量和方法
## 改动代码
* 最好限制在当前的方法/类, 不要对其他的产生影响。

# 可阅读代码

## 命名

### 好的命名习惯
* 常量：大写字母，单词间用下划线连接。如MAX_OPEN_FILES
* 静态变量：驼峰，以“s”开头。如sMaxOpenFiles
* 全局非静态变量:驼峰，以“m”开头。如mMaxOpenFiles

### 信息塞入名字
* 使用is和has这样的词来明确表示它是布尔值，避免使用反义的词
* 用min和max来表示极限
* 用first和last来表示包含的范围
* 用begin和end来表示包含/排除范围
想象别人看到这个名字的反映，用名字表达出意义。

### 一个栗子
private static final String EXTRA_STATE = "state";


## 代码风格

### 格式化
习惯于使用Android Studio快捷方式的开发，要配置代码格式，与team统一。

### 空行隔断
空行将一段代码分组。声明中以widiget分组，方法中操作对象分组等。

### 顺序一致
变量声明后，操作变量最好按照声明的顺序进行，清晰。

### Android Studio编辑界面好用的设置
* 显示行号
* 驼峰选择
* 命名前缀（s， m）
* 代码配色

### 工欲善其事，必先利其器。参考http://www.jianshu.com/p/4243f3b52644

## 注释

### 不对的想法
* 懒得写注释
* 先把所有的内容写完，回头再写注释
* 不负责任的写注释

### 合理的注释
* 明白注释的目的是帮助理解代码，打辅助的
* 将思考写出来，写有价值的注释
* 为什么这么写，代码的缺陷或可能引起的风险，防止被无用
* 变量，常量，方法等都需要

## 三目运算符
* 对代码上下文了解的情况下，使用三目运算符可节省代码行数
* 大量的if..else..可以用三目运算符代替
* 不要使理解变复杂
* 使用不当时，Android Studio编译器会有提示，根据提示和上下文修改

## 超长的表达式
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

## 变量及其作用域

### 减少临时变量

    if (item != null){
        /*int downloadId = item.mId;
        intent.putExtra("downloadId", downloadId);*/

        intent.putExtra("downloadId", item.mId);
    }

### 该私有的变量要private

## 公用代码
* 工具类
* 模板类
     
## 考虑写代码的影响

### 表达自己思考的同时要多想一些
* 时间成本
* 维护成本
* 内存影响、耗电影响、主线程影响
* 对代码上下文的影响

#### 数据传递
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

#### 字符串操作

SuggestionsAdapter.java:

    private void makePattern(String word) {
        try {
            pattern = Pattern.compile(word, Pattern.UNICODE_CASE | Pattern.CASE_INSENSITIVE);
        } catch (Exception e) {
            pattern = Pattern.compile("");
            e.printStackTrace();
        }
    }

#### 日志
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

## 最后
应用主流架构：MVP、MVVM
先研究，在独立的小模块可以先使用。


- 欢迎异议，给我指导！
