# 目录
1. [留言板](#留言板)
2. [爱心池](#爱心池抽奖)
3. [每日答题](#每日答题)

# 共享数据
```go
package export

// User 用户
type User struct {
	model.ModelCU

	ID       uint `json:"UserID" form:"UserID" gorm:"primarykey;autoIncrement"`
	SearchID uint `json:"SearchID" gorm:"unique;column:SearchID"` //搜索ID (唯一)(随机生成8位数字)

	AuthorID     string `json:"authorID" gorm:"index;column:authorID"`          //用户id
	FirstName    string `json:"firstName" gorm:"column:firstName"`              //首名
	LastName     string `json:"lastName" gorm:"column:lastName"`                //尾名
	NickName     string `json:"nickName" gorm:"index;column:nickName"`          //昵称
	IphoneNumber string `json:"iphoneNumber" gorm:"unique;column:iphoneNumber"` //手机号
	Avatar       string `json:"avatar" gorm:"column:avatar"`                    //头像

	Points        uint           `json:"points" gorm:"column:points"`                          //积分
	HistoryPoints []HistoryPoint `json:"historyPoints" gorm:"foreignKey:UserID;references:ID"` //历史总积分
}

type HistoryPoint struct {
	model.ModelC
	UserID     uint      `json:"UserID;" grom:"index;column:UserID"`                  //用户ID
	Change     uint      `json:"Change,omitempty" grom:"index;column:Change"`         //积分变动
	ChangeType TypePoint `json:"ChangeType,omitempty" grom:"index;column:ChangeType"` //变动类型
	ChangeText string    `json:"ChangeText,omitempty" grom:"index;column:ChangeText"` //变动原因
	Out        uint      `json:"Out,omitempty" grom:"index;column:Out"`               //当前积分
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

// TypeWhere 指定位置 0文章 1故事会 2留言墙评论 3故事会评论
type TypeWhere uint

const (
	TypeWhere_Work         TypeWhere = iota //文章
	TypeWhere_Story                         //故事会
	TypeWhere_WallComment                   //留言墙评论
	TypeWhere_StoryComment                  //故事会评论
)

// TypePoint 积分类型 0未知 1签到 2分享 3评论 4作品上传 5点赞 6新用户注册 7管理员操作 8购买
type TypePoint uint

const (
	TypePoint_Unknown TypePoint = iota
	TypePoint_SignIn            //签到
	TypePoint_Share             //分享
	TypePoint_Comment           //评论
	TypePoint_Work              //作品上传
	TypePoint_Like              //点赞
	TypePoint_NewUser           //新用户注册
	TypePoint_Admin             //管理员操作
	TypePoint_Buy               //购买
)

type BaseWork struct {
	model.ModelCD

	Title    string   `json:"Title,omitempty" gorm:"column:Title"`       //标题
	Text     string   `json:"Text,omitempty" gorm:"column:Text"`         //内容
	Img      []string `json:"Img,omitempty" gorm:"column:Img"`           //图片
	video    string   `json:"Video,omitempty" gorm:"column:Video"`       //视频地址
	videoImg string   `json:"VideoImg,omitempty" gorm:"column:VideoImg"` //视频首针图片

	LikeNum uint `json:"LikeNum,omitempty" gorm:"column:LikeNum"` //点赞数
	SaveNum uint `json:"SaveNum,omitempty" gorm:"column:SaveNum"` //收藏数量
	CommNum uint `json:"CommNum,omitempty" gorm:"column:CommNum"` //评论数量

	Other string `json:"Other" gorm:"column:Other"` //其他数据
}

// BaseComment 评论
type BaseComment struct {
	model.ModelCU

	UserID uint   `json:"UserID,omitempty" gorm:"column:UserID"` //用户
	Text   string `json:"Text,omitempty" gorm:"column:Text"`     //评论内容
	Like   uint   `json:"Like,omitempty" gorm:"column:Like"`     //点赞数

	Reply     uint `json:"Reply,omitempty" gorm:"column:Reply"`         //回复的消息
	ReplyData any  `json:"ReplyData,omitempty" gorm:"column:ReplyData"` //被回复的消息

	Block bool   `json:"Block,omitempty" gorm:"index;column:Block"` //是否屏蔽
	Other string `json:"Other,omitempty" gorm:"column:Other"`       //其他数据
}

// UserWallInfo 用户墙信息
type UserWallInfo struct {
	model.ModelCU
	ID uint `json:"UserWallInfoID" form:"UserWallInfoID" gorm:"primarykey;autoIncrement"`

	UserID uint `json:"UserID,omitempty"   gorm:"column:UserID"` //链接user基础信息

	SendWork   []uint `json:"SendWork,omitempty"  gorm:"serializer:json;column:SendWork"`     //发布的文章
	SaveWork   []uint `json:"SaveWork,omitempty"  gorm:"serializer:json;column:SaveWork"`     //收藏的文章
	DelayReply []uint `json:"DelayReply,omitempty"  gorm:"serializer:json;column:DelayReply"` //回复消息

	User *User  `json:"User" gorm:"foreignKey:UserID;references:ID"` //用户
	Save []Save `json:"Save" gorm:"foreignKey:SaveID;references:ID"` //收藏
}

// Work 作品
type Work struct {
	BaseWork
	ID uint `json:"WorkID" form:"WorkID" gorm:"primarykey;autoIncrement"`

	IsTop bool `json:"IsTop" gorm:"column:IsTop"` //是否置顶
	Block bool `json:"Block" gorm:"column:Block"` //是否屏蔽

	Recommend      bool `json:"Recommend" gorm:"column:Recommend"`           //是否推荐
	RecommendLevel uint `json:"RecommendLevel" gorm:"column:RecommendLevel"` //推荐等级

	Comm []WorkComment `json:"Comm" gorm:"foreignKey:WorkID;references:ID"` //评论

	UserID uint  `json:"UserID" gorm:"column:UserID"`                 //用户ID
	User   *User `json:"User" gorm:"foreignKey:UserID;references:ID"` //用户信息
}

// WorkComment 作品评论
type WorkComment struct {
	BaseComment
	ID uint `json:"WorkCommentID" form:"WorkCommentID" gorm:"primarykey;autoIncrement"`

	SaveNumber uint  `json:"SaveNumber,omitempty" gorm:"column:SaveNumber"` //收藏数
	WorkID     uint  `json:"WorkID,omitempty" gorm:"column:WorkID"`         //属于文章
	Work       *Work `json:"Work,omitempty" gorm:"column:Work"`             //文章
}

// Report 举报
type Report struct {
	model.ModelC
	ID uint `json:"ReportID" form:"ReportID" gorm:"primarykey;autoIncrement"`

	WorkID    uint      `json:"WorkID,omitempty" gorm:"column:WorkID"` //被举报的文章
	UserID    uint      `json:"UserID,omitempty" gorm:"column:UserID"` //举报人
	TypeWhere TypeWhere `json:"Type,omitempty" gorm:"column:Type"`     //举报位置
	Reason    string    `json:"Reason,omitempty" gorm:"column:Reason"` //举报原因

	Data any `json:"Data,omitempty"` //文章
}

// Recommend 推荐列表
type Recommend struct {
	model.ModelC
	ID uint `json:"RecommendID" form:"RecommendID" gorm:"primarykey;autoIncrement"` //推荐ID

	ShowHome bool `json:"ShowHome" gorm:"type:boolean;default:true;column:ShowHome"`                    //是否展示在首页
	WorkID   uint `json:"WorkID" gorm:"type:int;not null;column:WorkID"`                                //文章ID
	Work     Work `gorm:"foreignKey:WorkID;references:ID;constraint:OnUpdate:CASCADE,OnDelete:CASCADE"` //关联文章
}


// Story 故事会作品！
type Story struct {
	BaseWork
	ID uint `json:"StoryID" form:"StoryID" gorm:"primarykey;autoIncrement"`
	//期数
	Episode string `json:"Episode" gorm:"column:Episode"` //期数

	Comm []StoryComment `json:"Comm" gorm:"foreignKey:StoryID;references:ID"` //评论
}

// StoryComment 故事会评论
type StoryComment struct {
	BaseComment
	ID uint `json:"StoryCommentID" form:"StoryCommentID" gorm:"primarykey;autoIncrement"`

	SaveNumber uint   `json:"SaveNumber,omitempty" gorm:"column:SaveNumber"` //收藏数
	StoryID    uint   `json:"StoryID,omitempty" gorm:"column:StoryID"`       //属于文章
	Story      *Story `json:"Story,omitempty" gorm:"column:Story"`           //文章
}

// Save 收藏
type Save struct {
	model.ModelC
	ID uint `json:"SaveID" form:"SaveID" gorm:"primarykey;autoIncrement"`

	UserID uint      `json:"UserID,omitempty" gorm:"column:UserID"` //收藏人
	WorkID uint      `json:"WorkID,omitempty" gorm:"column:WorkID"` //被收藏的文章
	Work   Work      `json:"Work" gorm:"foreignKey:WorkID;references:ID"`
	Type   TypeWhere //类型
}

// ShieldingRules 屏蔽规则
type ShieldingRules struct {
	model.ModelCU
	ID uint `json:"ShieldingRulesID" form:"ShieldingRulesID" gorm:"primarykey;autoIncrement"`

	Using bool   `json:"Using,omitempty" gorm:"column:Using"` //是否启用
	Reg   string `json:"Reg,omitempty" gorm:"column:Reg"`     //正则表达式
}
```

## 接口
- 全局
    1. [x] 获取全局配置
       - 开关
         - 故事会
         - 发评论时间间限制 开关 以及时间间隔
         - 发文章时间间限制 开关 以及时间间隔
       - 爱心墙背景图地址
       - 故事会背景图地址
       - 我的背景图地址
       - 屏蔽规则
- 个人相关
  1. [x] 获取个人信息
      - 昵称 头像 搜索ID 积分
      - 自己发布的文章（前10篇）
  2. [x] 自己发布的文章(起始ID,排序类型)
  3. [x] 获取收藏夹
      - 收藏的文章
  4. [x] 获取别人评论自己的消息
        - 相关用户
        - 回复的评论
  5. [x] 获取积分变动记录(起始ID,排序类型)
- 留言墙
  1. [x] 获取留言墙列表（起始ID,排序类型）
      - 文章ID 标题 内容 图片 视频地址 视频首针图片 
      - 发布人ID 发布人昵称 发布人头像 发布时间 点赞数 收藏数 评论数 是否置顶
  2. [ ] 发布文章
      - 标题 内容
      - 图片或者视频地址
  3. [ ] 上传图片视频
      - 限制视频30S
      - 限制图片9张（压缩一波
- 故事会
    1. [ ] 获取故事会列表
        - 当前期号 标题
        - 历史期号 标题
    2. [ ] 获取故事会内容(期号)
        - 期号 标题 内容 图片 视频地址 视频首针图片
 - 共享接口
   1. [ ] 发表评论（文章ID，回复ID，内容,类型）
   2. [ ] 点赞（目标ID，类型）
   3. [ ] 收藏（目标ID，类型）
   4. [ ] 取消点赞（目标ID，类型）
   5. [ ] 取消收藏（目标ID，类型）
   6. [ ] 举报（目标ID，类型，原因）
   7. [ ] 删除（目标ID，类型）只能删除自己的文章和评论
   8. [ ] 获取指定文章（文章ID，类型）
      - 文章ID 标题 内容 图片 视频地址 视频首针图片 
      - 发布人ID 发布人昵称 发布人头像 发布时间 点赞数 收藏数 评论数 是否置顶
      - 评论前50条
   9. [ ] 获取指定评论（文章ID，文章类型,起始评论ID）
      - 50条评论

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
## 接口
- 用户
    1. [ ] 获取用户信息
        - 今日答题内容
            - 题目 答案 选择的
        - 昵称 头像
- 答题
    1. [ ] 获取题目
        - 题目 选项
    2. [ ] 提交答案
    
    
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
