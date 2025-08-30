# 目录
1. [留言板](#留言板)
2. [爱心池](#爱心池抽奖)
3. [每日答题](#每日答题)

# 共享数据
```go
//用户总信息
type user {
    ID uint  //索引用ID
    CreateAt time.time //创建时间
    SreachID uint //搜索用

    PhoneNumber string //手机号
    NickName string //昵称
    UserName string //用户名称

    Points uint //积分
}

type histroy{
    ID uint 
    CreateAt time.time
    UserID uint 
    Change uint //积分变动
    ChangeText string //变动原因 
    out uint //当前积分
}
```

# 留言板

## 界面
底部菜单选择栏
- 首页
    1. 首页
        - 顶部图片
        - 一级菜单
            1. 感恩留言墙
                - 菜单
                    1. 最新发布
                        - 栏目
                            1. 标题
                            2. 官方标识
                            2. 内容
                            3. 昵称
                            4. 头像
                            5. 点赞（数量） 评论（数量）收藏（数量）
                    2. 精选榜
                        - 栏目（同上）
                    3. 点赞榜
                        - 栏目(同上)
                - 发布(右下角悬浮按钮)
                    1. 标题
                    2. 内容 限制500字以内
                    3. 上传图片9照以内 或者 视频30S以内
                    4. 发布按钮（确认发布
            2. 光荣故事会
                - 菜单
                    1. 最新发布
                    2. 最佳榜
                    3. 热门榜
                    4. 往期榜(都是同上)
                - 发布(同上)
- 我的
    1. 图片
        - 头像 昵称 搜索ID
    3. 栏目
        1. 收藏夹(新页面)
        2. 消息通知(新页面)
    4. 积分
        - 新页面 积分明细


## 功能
1. 获取全局配置
    - 开关 历史推荐
    - 首页背景图地址
    - 我的背景图地址
## 数据库
```go
// 留言区补充用户信息
type Wall {
    ID uint //索引用
    UserID uint //链接user基础信息

    SendWork []uint //发布的文章
    SaveWork []uint //收藏的文章
    Reply []uint //回复消息
}

type Work {
    ID uint //索引用
    CreateAt time.time //创建时间
    DeleteAt time.time //删除时间 删除 自己删自己的 或者控制台删除 更改为删除重新写

    UserID uint //发布人
    Type uint //类型 0爱心留言区 1光荣故事会

    Title,Text string //标题与内容
    Img []string //图片
    video string //视频地址
    videoImg string //视频首针图片

    IsTop bool //是否置顶

    Like uint //点赞数
    Save uint //收藏数量
    Comm uint //评论数量

    Comm []Comm //评论
}

type Comm {
    ID uint //索引

    WrokID uint //属于文章
    Reply uint //回复的消息

    Like uint //点赞

    Title,Text string //标题以及内容
}

type 屏蔽{
    ID uint 
    Reg string
}
```
# 爱心池（抽奖
## 页面
1. 图片
2. 头像 昵称 爱心点(拉一个人一个爱心点)(兑换按钮) 卡包(爱心点兑换卡号(8位数))
    - 卡包（新页面
        - 返回键 卡包标题
        - 期数 总计 折叠栏
            - 卡号 文本(几等奖) （中奖整行红 没中灰 未开绿 中奖了置顶
3. 爱心互助池
    - 左侧 上期中奖名单
    - 中间 爱心 （百分比填充 从下往上
    - 底部 文本 差多少爱心点开奖
4. 奖品预览
    - 九宫格（图片
5. 规则 文本

## 数据
```go
type userinfo{
    ID uint //索引
    UserID uint //挂接

    LoveNumber uint //爱心点
}

type 期数{ //三位数
    ID uint // 50左右开始
    中奖的 {
        奖1-4：[]uint //中奖的人的id
    }
    总爱心点 uint //显示的爱心点
    目标爱心点 uint 
}

type care{
    ID uint //索引
    UserID uint //用户id
    Number string //卡号
    是否中奖 uint //是否中奖 0未中奖 1一等奖 2二等奖 
}
```
# 每日答题
## 界面
- 顶部图片
    - 头像 用户名
- 主题 文本
- 中间一个开始按钮
- 题目 3题（ab）
- 提交

## 数据
```go
user {
    ID uint 
    UserID uint 
    UpDateAt time.time //更新日期

    题目 []题库
    选择答案 []uint
}

type 题库{
    ID uint
    问题 string
    选项 []string
    答案 uint
}
```
# 方式
前端上传im

后端自己部署
