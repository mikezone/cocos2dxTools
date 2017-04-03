# Code Style
## 命名
### 文件和文件夹命名
使用和cocos一样的C++命名风格，但不使用缩写：

| 类型 | 一个单词 | 多个单词 |
| --- | --- | --- |
| 文件夹 | 单词全拼、全小写 | 大驼峰 |
| 文件 | 类名：大驼峰。其余：单词全拼、全小写| 大驼峰 |

### 变量名

| 类型 | 一般规则 | 特殊情况 |
| --- | --- | --- |
| 全局变量 | 不建议使用 |  |
| 临时变量 | 小驼峰 |  |
| 类成员变量 | 匈牙利命名：m + 功能 + 类型 例如：mNameText |  |

变量名命名避免歧义，几种常见的产生歧义的情况：

| 例子 | 分析|
| --- | --- |
| temp | 只知道是一个用完丢掉的变量，但不知道它具体是什么作用的变量，应该至少加上类型或者代表功能性的单词，如tempLabel |
| abc | 没有任何意义的命名 |
| listView | 对于整个作用域中只有一个listView的情况来说没有什么问题，如果当一个页面中有多个listView，这种命名就会产生歧义，应该加上功能性描述单词，如roomListView |


### 函数名

所有的方法使用小驼峰命名规则，对于类成员方法一般规则是，动作 + 施加对象。常见情况如下：

|  情况 |  说明 | 举例 |
| --- | --- | --- |
| 一般方法 | 表语 动作 + 限定词(可选) + 宾语<br/>动作 + 多宾语<br/>动作 + 宾语 + 次宾语 (可选)<br/>动作 + 宾语 + 补语 (可选) | isRunning getLocalZOrder<br/>stepPhysicsAndNavigation<br/>giveYouAnApple<br/>addChildToPhysicsWorld |
|  响应事件的方法 | on + 事件，其中事件又可以细化为：动作 + 施加对象、施加对象 + 被动词，因此主要有两种规则： <br/>on + 动作 + 施加对象<br/>on + 施加对象 + 分词| onClickJoinButton onKeyPressed |

## 行文排版
快代码、类声明、方法实现使用的`{}`均使用Java风格。
### 类
先写public方法，再写宏，再写private属性。如：

```cpp
class HelloWorldLayer : public cocos2d::Layer {
public:
    static cocos2d::Scene* createScene();
    virtual bool init();
    CREATE_FUNC(HelloWorldLayer);
private:
    int mIndex;
};

```
### 宏
1.多行代码一定使用`\`续行
2.如果具有函数功能的宏，尽量使用 `do {} while(0)`而不要另外定义函数

### 注释、空格、空行
#### 注释
单行注释：注释符`//`之后必紧跟一个空格
多行注释：一般只用于文档注释
文档注释：参看[http://www.cnblogs.com/Mike-zh/p/5136885.html](http://www.cnblogs.com/Mike-zh/p/5136885.html)
#### 空格
方法实现处：`{`之前
赋值语句：`=`前后
#### 空行
方法实现之间必有一个空行
引用头文件`#include`之后必有一个空行
使用命名空间后必有一个空行
任何地方都不应该连空两行，pragma注释行前后均有一个空行，如

```cpp
#include <stdio.h>
#include "HelloScene.h"

using namespace std;
US_NS_CC;

bool HelloScene::init() {
    // ...
    return true;
}

#pragma mark - private

void HelloScene::setUserInfo() {
    // ...
}

#pragma mark - event handler

void HelloScene::onTouchValidGameButton(Ref* ref, Widget::TouchEventType eventType){
    // ...
}

void HelloScene::onKeyPressed(EventKeyboard::KeyCode keyCode, cocos2d::Event *event) {
    // ...
}
```
## 代码内容
旧的代码风格要抛弃，尽量使用新规范
### 枚举
使用C++ 11中枚举的定义风格

```cpp
enum class Color {
    RED, BLUE, GREEN
};
```
这样可以
1.避免需要传入枚举的地方误传了其他整型
2.对枚举类型进行作用域保护，避免相近语义的枚举类型混淆。
### 内联函数
类内联函数, 尽量新建.inl文件
普通内联函数 适配C语言和各系统版本，

```cpp
#if !defined(NS_INLINE)
    #if defined(__GNUC__)
        #define NS_INLINE static __inline__ __attribute__((always_inline))
    #elif defined(__MWERKS__) || defined(__cplusplus)
        #define NS_INLINE static inline
    #elif defined(_MSC_VER)
        #define NS_INLINE static __inline
    #elif TARGET_OS_WIN32
        #define NS_INLINE static __inline__
    #endif
#endif
```
### virtual函数、虚基类
将虚基类按照Java接口的方式定义：以字母`I`开头，集成它的子类必须在实现方法前继续沿用`virtual`关键字，并且在后面添加`override`关键字。

```cpp
class IAction {
public:
    virtual void run() = 0;
    virtual void sleep() = 0;
};

class Person: public IAction {
public:
    virtual void run() override;
    virtual void sleep() override;
};
```
如果继承了带有普通虚函数的类，重写函数时仍需要`virtual`关键字，并且在后面添加`override`关键字，

```cpp
class Animal {
public:
    virtual void run();
    virtual void sleep();
};

class Person: public Animal {
public:
    virtual void run() override;
    virtual void sleep() override;
};
```
### 条件编译 and 自定义宏
#### 条件编译
每个头文件的声明内容要添加条件宏：

```cpp
#ifndef __HELLOWORLD_SCENE_H__
#define __HELLOWORLD_SCENE_H__

USING_NS_CC;
using namespace cocos2d::ui;

class HelloWorldScene : public Scene {
};

#endif // __HELLOWORLD_SCENE_H__
```
这个条件宏的命名规则是: __ + 全大写Unix命名 + __ (两个`_`)。

#### 自定义的宏
对于自定义的宏，遵循以下书写规则：
1.尽量放到命名空间中
2.使用全大写Unix命名
3.语义明确

```cpp
NS_CC_BEGIN // 1.尽量放到命名空间中

// 2.使用全大写Unix命名
// 3.语义明确:这是一个生成create方法的宏，传入的参数为类型
#define CREATE_FUNC(__TYPE__) \
static __TYPE__* create() \
{ \
    __TYPE__ *pRet = new(std::nothrow) __TYPE__(); \
    if (pRet && pRet->init()) \
    { \
        pRet->autorelease(); \
        return pRet; \
    } \
    else \
    { \
        delete pRet; \
        pRet = nullptr; \
        return nullptr; \
    } \
}

NS_CC_END
```
### 命名空间、作用域
对命名空间进行条件编译，适配C语言和C++,

```cpp
#ifdef __cplusplus
    #define NS_CC_BEGIN                     namespace cocos2d {
    #define NS_CC_END                       }
    #define USING_NS_CC                     using namespace cocos2d
    #define NS_CC                           ::cocos2d
#else
    #define NS_CC_BEGIN 
    #define NS_CC_END 
    #define USING_NS_CC 
    #define NS_CC
#endif
```
### cocos内存管理
使用cocos内存管理要遵守以下规则：
1.继承自Ref，这样可以使用Ref类的成员变量进行引用计数管理和在指针销毁的时候自动调用release方法。
2.使用HardCoding的create方法创建类，如果new 对象成功返回则使用aurorelease方法将对象添加到内存管理池中。因此这里最重要的不是使用create方法，而是在new成功之后要调用一次autorelease。
使用cocos进行内存管理之后，所有对对象的操作都使用指针。
#### 如果不使用cocos内存管理
目前已知需要进行手动内存管理的地方：
1.自定义一种类型而不继承自Ref
2.C++标准库中的类
3.分配到堆内存中的普通类型(int ...)
遇到这些情况时，尽量使用智能指针。

