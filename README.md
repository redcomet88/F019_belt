# F019 vue+flask海外购商品推荐可视化分析系统一带一路【三种推荐算法】

> 完整项目收费，可联系QQ: 81040295 微信: mmdsj186011 注明从git来的，谢谢！
也可以关注我的B站： 麦麦大数据 https://space.bilibili.com/1583208775
> 
B站up： **麦麦大数据**
关注B站，有好处！
编号:  F019
关键词：海外购 推荐系统 一带一路 python
## 视频

[video(video-CKHDGXqA-1757921292451)(type-bilibili)(url-https://player.bilibili.com/player.html?aid=556648304)(image-https://i-blog.csdnimg.cn/img_convert/f25e69bd4edb3ca8199b1430b146c011.jpeg)(title-Vue+Flask 海外购电商大数据推荐系统源码 （三种推荐算法 + 全新界面布局）)]

## 1 系统简介
系统简介：本系统是一个基于Vue+Flask构建的一带一路国家进口商品推荐可视化系统，其核心功能围绕商品数据的展示、智能推荐和用户管理展开。主要包括：首页，用于展示系统概览和轮播图；商品推荐模块，利用UserCF、ItemCF和SVD三种算法为用户提供个性化的商品推荐；数据可视化模块，通过丰富的图表展示进出口商品统计、一级分类分析（柱状图）、分类统计（饼图）、店铺分析（环图）和进口国家分析（漏斗图），为用户提供直观的数据分析；表格功能，允许用户查看详细的商品信息；以及用户管理模块，包含登录与注册功能，和个人设置（允许用户修改个人信息、头像及密码），确保系统的安全性和个性化体验。
## 2 功能设计
该系统采用典型的B/S（浏览器/服务器）架构模式。用户通过浏览器访问Vue前端界面，该前端由HTML、CSS、JavaScript以及Vue.js生态系统中的Vuex（用于状态管理）、Vue Router（用于路由导航）和Echarts（用于数据可视化）等组件构建。前端通过API请求与Flask后端进行数据交互，Flask后端则负责业务逻辑处理，并利用SQLAlchemy（或类似ORM工具）与MySQL数据库进行持久化数据存储。此外，系统还包含一个独立的数据抓取模块，负责从外部来源抓取商品数据并将其导入MySQL数据库，为整个系统提供数据支撑。
### 2.1系统架构图
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/b4dcb3955a4049949e232f1c9b682187.png)
### 2.2 功能模块图
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/295a3b13395b47c8a8b0f51bc20597e7.png)
## 3 功能展示
### 3.1 登录 & 注册
登录注册做的是一个可以切换的登录注册界面，点击去登录后者去注册可以切换，背景是一个视频，循环播放。
登录需要验证用户名和密码是否正确，如果**不正确会有错误提示**。
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/babc8d5589e64d1390e4976fec09c0d6.png)

注册需要**验证用户名是否存在**，如果错误会有提示。
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/8d6d138449fe46628c6a0166a73b8de3.png)
### 3.2 主页
主页的布局采用了上侧是菜单，下侧是操作面板的布局方法，右侧的上方还有用户的头像和退出按钮，如果是新注册用户。
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/57df4fd6da544320872513bc410a360c.png)
### 3.3 数据分析
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/3df81809cd474fb38cafb51d05f4b60d.png)
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/69e89909235e449f884276413d3b81b1.png)
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/9c26ba0b22a34b7195485301b34b5c60.png)
### 3.4 商品信息
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/f3c54fede5a649018399f01a42a59d1c.png)
### 3.5 三推荐算法推荐商品

UserCF推荐算法：
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/f49d3504cdd6474793f47854475caedc.png)

ItemCF推荐算法：
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/1d7cf9e07ccd488e890328c54ef781b6.png)

SVD推荐算法：
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/3a9c9a4a742147a0a96bdf53b2c1a692.png)
## 4程序代码
### 4.1 代码说明
代码介绍：以下是一个基于用户协同过滤(UserCF)的一带一路进口商品推荐系统的Python实现。该算法通过分析用户的历史行为数据，为每个用户提供个性化的商品推荐。系统首先计算用户之间的相似度矩阵，然后根据相似度矩阵为每个用户推荐最相关的商品。
### 4.2 流程图
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/e676e484d14449709925bd1748c42041.png)
### 4.3 代码实例
```python
import pandas as pd
import numpy as np
from sklearn.metrics.pairwise import cosine_similarity

def build_user_item_matrix(data, user_col='user_id', item_col='item_id', rating_col='rating'):
    # 构建用户-商品矩阵
    user_item_matrix = data.pivot(index=user_col, columns=item_col, values=rating_col).fillna(0)
    return user_item_matrix

def compute_user_similarity(user_item_matrix):
    # 计算用户-用户相似度矩阵
    user_similarity = cosine_similarity(user_item_matrix)
    user_similarity_df = pd.DataFrame(user_similarity, index=user_item_matrix.index, columns=user_item_matrix.index)
    return user_similarity_df

def recommend_items(user_id, user_item_matrix, user_similarity_df, top_n=10):
    # 找到与目标用户相似的用户
    similar_users = user_similarity_df[user_id].sort_values(ascending=False)[1:]
    
    # 提取相似用户的购买记录
    recommended_items = pd.Series(dtype=float)
    for similar_user, similarity in similar_users.items():
        items = user_item_matrix.loc[similar_user]
        score = items * (similarity)
        recommended_items = recommended_items.add(score, fill_value=0)

    # 过滤掉用户已经购买的商品
    already_bought = user_item_matrix.loc[user_id]
    recommended_items = recommended_items[already_bought == 0]

    # 返回推荐商品列表
    recommended_items = recommended_items.sort_values(ascending=False).head(top_n)
    return recommended_items.index.tolist()

# 示例数据
data = pd.DataFrame({
    'user_id': [1, 1, 2, 2, 3, 4],
    'item_id': ['A', 'B', 'B', 'C', 'A', 'D'],
    'rating': [5, 4, 3, 2, 5, 3]
})

user_item_matrix = build_user_item_matrix(data)
user_similarity_df = compute_user_similarity(user_item_matrix)

# 推荐用户1的商品
recommendations = recommend_items(1, user_item_matrix, user_similarity_df)
print("推荐给用户1的商品:", recommendations)

```

