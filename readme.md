# 目录
1. [留言板](#留言板)
2. [爱心池](#爱心池抽奖)
3. [每日答题](#每日答题)

# 共享数据
```go
package export

import "time"
//用户总信息
type User struct{
    ID uint  //索引用ID
    CreateAt time.Time //创建时间
    SreachID uint //搜索用

    PhoneNumber string //手机号
    NickName string //昵称
    UserName string //用户名称

    Points uint //积分
	HistoryPoints []HistoryPoints //积分变动记录
}

type HistoryPoints struct{
    ID uint 
    CreateAt time.Time
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



## 数据库
```go
package export

import "time"
// 留言区补充用户信息
type WallInfo struct{
    ID uint //索引用
    UserID uint //链接user基础信息

    SendWork []uint //发布的文章
    SaveWork []uint //收藏的文章
    Reply []uint //回复消息
	
	User *User //用户
}

type Work struct{
    ID uint //索引用
    CreateAt time.Time //创建时间
    DeleteAt time.Time //删除时间 删除 自己删自己的 或者控制台删除 更改为删除重新写

    UserID uint //发布人
    Type uint //类型 0爱心留言区 1光荣故事会

    Title,Text string //标题与内容
    Img []string //图片
    video string //视频地址
    videoImg string //视频首针图片

    IsTop bool //是否置顶

    LikeNum uint //点赞数
    SaveNum uint //收藏数量
    CommNum uint //评论数量

    Comm []Comment //评论
}

// Comment 评论
type Comment struct{
    ID uint //索引

    WorkID uint //属于文章
    Reply uint //回复的消息

    Like uint //点赞

    Text string //标题以及内容
	
	Work *Work //文章
	Comment *Comment //回复的消息
}

// ShieldingRules 屏蔽规则
type ShieldingRules struct{
    ID uint 
    Reg string
}
```

## 接口
- 全局
    1. [ ] 获取全局配置
       - 开关 历史推荐
       - 首页背景图地址
       - 我的背景图地址
- 个人相关
  1. [ ] 获取个人信息
      - 昵称 头像 搜索ID 积分
      - 自己发布的文章（前10篇）
  2. [ ] 获取收藏夹
      - 收藏的文章
  3. [ ] 获取未读消息
        - 相关文章
        - 相关用户
        - 回复的评论
  4. [ ] 获取积分变动记录(起始ID,排序类型)
- 留言墙
  1. [ ] 获取留言墙（起始ID,排序类型）
      - 文章ID 标题 内容 图片 视频地址 视频首针图片 发布人ID 发布人昵称 发布人头像 发布时间 点赞数 收藏数 评论数 是否置顶
      - 前50 评论
  2. [ ] 获取评论(文章ID,起始ID)
  3. [ ] 发布文章
      - 标题 内容
      - 图片或者视频地址
  4. [ ] 上传图片视频
      - 限制视频30S
      - 限制图片9张（压缩一波
  5. [ ] 发表评论（文章ID，回复ID，内容）

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
package export

import "time"

type UserInfo struct {
	
    ID uint //索引
    UserID uint //挂接

    LoveNumber uint //爱心点

	User *User //用户
}
// Period 周期
type Period struct{ //三位数
    ID uint // 50左右开始
	CreateAt time.Time //创建时间
    WinList Win //中奖名单
    LovePointNum uint //显示的爱心点
	TargetPointNum  uint //目标爱心点
	
	Care []Card //卡包
}

// Win 中奖名单
type Win struct {
	One []Card
	Two []Card
	Three []Card
	Four []Card
}

// Card 卡包
type Card struct{
    ID uint //索引
    UserID uint //用户id
	PeriodID uint //期数id
    Number string //卡号
    IsWin uint //是否中奖 0未中奖 1一等奖 2二等奖 
	
	User *User //用户
}
```

## 接口
- 获取全局信息
    1. [ ] 配置信息
        - 9张图片地址
        - 本期
          - 当前爱心点
          - 目标爱心点
          - 中奖名单
            - 几等奖 卡号 用户昵称
        - 个人信息
            -头像 昵称 爱心点
- 个人信息
    1. [ ] 获取历史期数
        - 期数
            - 卡号 几等奖 显示20个
    2. [ ] 获取卡号（期数，第几页）

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
package export

import "time"
// User 用户
type User struct {
    ID uint 
    UserID uint 
    UpDateAt time.Time //更新日期

	Question []QuestionBank
    Option []uint
}

// QuestionBank 题库
type QuestionBank struct{
    ID uint
	Question string //题目
	Option []string //选项
	Answer uint //答案
	Type uint //类型 对接Question
}

type Question struct{
	ID uint 
	Title string //标题
}
```

# 后台电话池
1. 导入数据
2. 员工抽取数据
3. 设置 接通（标记1） 未接（设置0 且释放） 空号（2）
4. 员工 接通率 总池子接通率

## 数据库
```go
池子{
	id uint 
	name string //名字
	phone string //手机号
	
	useID uint //使用人
	res uint // 0未使用 1接通 2空号
	未接次数 uint //未接次数
}
个人统计 {
	id uint 
	createAt time.time //创建时间
    userID uint //用户id
    池子iD uint //池子id
	
	type uint //类型 0抽取 1接通 2未接 3空号 4释放
	
	time time.time //时间
}
每日统计{
	id uint 
    createAt time.time //创建时间
    userID uint //用户id
    
    抽取 uint
    接通 uint
    未接 uint
    空号 uint
	退回 uint
	总计数量 uint
}
月统计{
    id uint
    createAt time.time //创建时间
    userID uint //用户id
    
    抽取 uint
    接通 uint
    未接 uint
    空号 uint
    退回 uint
	总计数量 uint
}
```

# 方式
前端上传im

后端自己部署
