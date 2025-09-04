# 道具SaaS平台 Ant Design UI设计方案

## 版本信息
- 版本：1.1.0
- 创建时间：2025-09-04
- 更新时间：当前日期
- 负责人：UI设计师
- 状态：优化中

## 一、设计原则与规范

### 1.1 Ant Design组件选型原则
- **简洁专业**：仅使用Ant Design标准组件，避免过度定制
- **功能匹配**：严格按照PRD需求范围进行组件选择
- **一致性**：保持整体设计语言统一
- **可维护性**：优先选择稳定、文档完善的组件

### 1.2 色彩规范
```css
/* 主色调 */
--primary-color: #1890ff;        /* Ant Design 默认蓝色 */
--success-color: #52c41a;        /* 成功状态 */
--warning-color: #faad14;        /* 警告状态 */
--error-color: #f5222d;          /* 错误状态 */

/* 中性色 */
--text-color: #262626;           /* 主文本 */
--text-color-secondary: #8c8c8c; /* 次要文本 */
--border-color: #d9d9d9;         /* 边框色 */
--background-color: #f0f2f5;     /* 背景色 */
```

### 1.3 间距规范
- **基础间距单位**：8px
- **组件间距**：16px、24px
- **页面边距**：24px
- **卡片内边距**：16px、24px

## 二、整体布局设计

### 2.1 优化后的主布局结构（Layout组件）
```jsx
<Layout style={{ minHeight: '100vh' }}>
  {/* 侧边栏 - 采用层级化设计 */}
  <Sider 
    width={256} 
    theme="light" 
    collapsible
    style={{
      background: 'linear-gradient(180deg, #f0f2f5 0%, #ffffff 100%)',
      borderRight: '1px solid #e8e8e8'
    }}
  >
    {/* Logo区域 - 顶层标识 */}
    <div className="logo-section" style={{
      height: 64,
      background: 'linear-gradient(135deg, #667eea 0%, #764ba2 100%)',
      display: 'flex',
      alignItems: 'center',
      justifyContent: 'center',
      color: '#fff',
      fontWeight: 'bold'
    }}>
      道具SaaS平台
    </div>
    
    {/* 层级化导航菜单 */}
    <Menu 
      mode="inline" 
      style={{ 
        borderRight: 0,
        background: 'transparent'
      }}
      items={hierarchicalMenuItems}
    />
  </Sider>
  
  <Layout>
    {/* 优化后的顶部导航 */}
    <Header style={{ 
      background: '#fff', 
      padding: '0 24px',
      borderBottom: '1px solid #e8e8e8',
      display: 'flex',
      alignItems: 'center',
      justifyContent: 'space-between'
    }}>
      <div className="header-left" style={{ display: 'flex', alignItems: 'center', gap: 16 }}>
        {/* 三级面包屑导航 */}
        <Breadcrumb 
          items={generateHierarchicalBreadcrumb()}
          style={{ fontSize: '14px' }}
        />
      </div>
      
      <div className="header-right" style={{ display: 'flex', alignItems: 'center', gap: 16 }}>
        {/* 应用切换器 - 移至右侧 */}
        <ApplicationSwitcher />
        
        {/* 通知和用户菜单 */}
        <Badge count={5}>
          <Button type="text" icon={<BellOutlined />} />
        </Badge>
        <Dropdown overlay={userMenu}>
          <Space style={{ cursor: 'pointer' }}>
            <Avatar icon={<UserOutlined />} />
            <span>管理员</span>
          </Space>
        </Dropdown>
      </div>
    </Header>
    
    {/* 主内容区 */}
    <Content style={{ 
      margin: '24px',
      background: '#fff',
      borderRadius: '8px',
      boxShadow: '0 2px 8px rgba(0,0,0,0.1)'
    }}>
      <ApplicationContextProvider>
        {children}
      </ApplicationContextProvider>
    </Content>
  </Layout>
</Layout>
```

### 2.2 应用上下文管理
```jsx
// 应用上下文提供者
const ApplicationContextProvider = ({ children }) => {
  const [currentMerchant, setCurrentMerchant] = useState(null);
  const [currentApplication, setCurrentApplication] = useState(null);
  const [availableApplications, setAvailableApplications] = useState([]);

  // 当商户切换时，重新加载应用列表
  useEffect(() => {
    if (currentMerchant) {
      loadApplicationsByMerchant(currentMerchant.merchant_id);
    }
  }, [currentMerchant]);

  const contextValue = {
    currentMerchant,
    setCurrentMerchant,
    currentApplication,
    setCurrentApplication,
    availableApplications,
  };

  return (
    <ApplicationContext.Provider value={contextValue}>
      {children}
    </ApplicationContext.Provider>
  );
};
```

### 2.3 响应式断点
- **xs**: <576px
- **sm**: ≥576px
- **md**: ≥768px
- **lg**: ≥992px
- **xl**: ≥1200px
- **xxl**: ≥1600px

## 三、系统架构设计

### 3.1 商户-应用-模块三级架构

#### 3.1.1 架构层级关系
```
道具SaaS平台
├── 商户层（Merchant Level）
│   ├── 商户管理
│   ├── 商户概览仪表板
│   └── 应用层（Application Level）
│       ├── 应用管理
│       ├── 应用切换器
│       └── 模块层（Module Level）
│           ├── 道具模板管理
│           ├── 用户背包系统
│           └── 数据统计功能
```

#### 3.1.2 层级化导航结构设计
```jsx
// 层级化侧边栏导航结构
const hierarchicalMenuItems = [
  // 商户层 - 顶层管理功能
  {
    key: 'merchant',
    icon: <UserOutlined />,
    label: '商户管理',
    style: {
      background: 'linear-gradient(135deg, #667eea 0%, #764ba2 100%)',
      color: '#fff',
      marginBottom: '8px',
      borderRadius: '6px'
    },
    children: [
      { 
        key: '/merchant/overview', 
        label: '商户概览',
        style: { paddingLeft: '48px' }
      },
      { 
        key: '/merchant/list', 
        label: '商户列表',
        style: { paddingLeft: '48px' }
      },
    ],
  },
  
  // 应用层 - 核心二级功能
  {
    key: 'application',
    icon: <AppstoreOutlined />,
    label: '应用管理',
    style: {
      background: 'linear-gradient(135deg, #f093fb 0%, #f5576c 100%)',
      color: '#fff',
      marginBottom: '8px',
      borderRadius: '6px'
    },
    children: [
      { 
        key: '/application/overview', 
        label: '应用概览',
        style: { paddingLeft: '48px' }
      },
      { 
        key: '/application/list', 
        label: '应用列表',
        style: { paddingLeft: '48px' }
      },
    ],
  },
  
  {
    type: 'divider',
    style: { margin: '16px 0' }
  },
  
  // 模块层 - 三级功能模块
  {
    key: 'modules',
    icon: <SettingOutlined />,
    label: '应用模块',
    style: {
      background: 'linear-gradient(135deg, #4facfe 0%, #00f2fe 100%)',
      color: '#fff',
      marginBottom: '8px',
      borderRadius: '6px'
    },
    children: [
      {
        key: '/modules/item-template',
        icon: <GiftOutlined />,
        label: '道具模板管理',
        style: { paddingLeft: '48px' }
      },
      {
        key: '/modules/user-bag',
        icon: <InboxOutlined />,
        label: '用户背包系统',
        style: { paddingLeft: '48px' }
      },
      {
        key: '/modules/statistics',
        icon: <BarChartOutlined />,
        label: '数据统计功能',
        style: { paddingLeft: '48px' }
      },
    ],
  },
];

// 三级面包屑生成函数
const generateHierarchicalBreadcrumb = () => {
  const pathname = usePathname();
  const { currentMerchant, currentApplication } = useApplicationStore();
  
  const breadcrumbItems = [
    {
      title: (
        <Space>
          <UserOutlined style={{ color: '#667eea' }} />
          <span>{currentMerchant?.merchant_name || '选择商户'}</span>
        </Space>
      ),
      href: '/merchant/overview',
    },
  ];
  
  // 应用层面包屑
  if (pathname.includes('/application') || pathname.includes('/modules')) {
    breadcrumbItems.push({
      title: (
        <Space>
          <AppstoreOutlined style={{ color: '#f5576c' }} />
          <span>应用管理</span>
        </Space>
      ),
      href: '/application/overview',
    });
  }
  
  // 当前应用信息
  if (currentApplication && pathname.includes('/modules')) {
    breadcrumbItems.push({
      title: (
        <Space>
          <Tag color="blue" size="small">{currentApplication.app_name}</Tag>
        </Space>
      ),
    });
  }
  
  // 模块层面包屑
  if (pathname.includes('/modules/')) {
    const moduleMap = {
      'item-template': { name: '道具模板管理', icon: <GiftOutlined />, color: '#4facfe' },
      'user-bag': { name: '用户背包系统', icon: <InboxOutlined />, color: '#4facfe' },
      'statistics': { name: '数据统计功能', icon: <BarChartOutlined />, color: '#4facfe' },
    };
    
    const moduleKey = pathname.split('/modules/')[1];
    const module = moduleMap[moduleKey];
    
    if (module) {
      breadcrumbItems.push({
        title: (
          <Space>
            {React.cloneElement(module.icon, { style: { color: module.color } })}
            <span>{module.name}</span>
          </Space>
        ),
      });
    }
  }
  
  return breadcrumbItems;
};
```

#### 3.1.3 优化后的应用切换器组件
```jsx
// 顶部右侧应用切换器 - 层级化设计
const ApplicationSwitcher = () => {
  const { currentApplication, setCurrentApplication, availableApplications } = useApplicationStore();
  const [dropdownVisible, setDropdownVisible] = useState(false);

  const handleApplicationChange = (app) => {
    setCurrentApplication(app);
    setDropdownVisible(false);
    message.success(`已切换到应用：${app.app_name}`);
  };

  const applicationMenu = {
    items: availableApplications.map(app => ({
      key: app.app_id,
      label: (
        <div 
          style={{ 
            padding: '8px 12px',
            borderRadius: '6px',
            background: currentApplication?.app_id === app.app_id 
              ? 'linear-gradient(135deg, #4facfe 0%, #00f2fe 100%)' 
              : 'transparent',
            color: currentApplication?.app_id === app.app_id ? '#fff' : '#333'
          }}
          onClick={() => handleApplicationChange(app)}
        >
          <Space>
            <Avatar 
              size="small" 
              icon={<AppstoreOutlined />}
              style={{
                background: currentApplication?.app_id === app.app_id 
                  ? 'rgba(255,255,255,0.2)' 
                  : '#f0f2f5'
              }}
            />
            <div>
              <div style={{ fontWeight: 'bold' }}>{app.app_name}</div>
              <div style={{ 
                fontSize: '12px', 
                opacity: 0.8,
                color: currentApplication?.app_id === app.app_id ? '#fff' : '#999'
              }}>
                ID: {app.app_id}
              </div>
            </div>
            {app.status === 1 ? (
              <Tag color="success" size="small">启用</Tag>
            ) : (
              <Tag color="error" size="small">禁用</Tag>
            )}
          </Space>
        </div>
      ),
    })),
  };

  return (
    <div className="app-switcher" style={{ display: 'flex', alignItems: 'center', gap: 12 }}>
      {/* 当前应用显示 */}
      {currentApplication ? (
        <Space>
          <Tag 
            color="blue" 
            style={{ 
              background: 'linear-gradient(135deg, #4facfe 0%, #00f2fe 100%)',
              border: 'none',
              color: '#fff',
              fontWeight: 'bold'
            }}
          >
            <AppstoreOutlined /> {currentApplication.app_name}
          </Tag>
        </Space>
      ) : (
        <Tag color="default" style={{ cursor: 'pointer' }}>
          <AppstoreOutlined /> 请选择应用
        </Tag>
      )}
      
      {/* 应用切换下拉菜单 */}
      <Dropdown
        menu={applicationMenu}
        trigger={['click']}
        open={dropdownVisible}
        onOpenChange={setDropdownVisible}
        placement="bottomRight"
        overlayStyle={{ minWidth: 280 }}
      >
        <Button 
          type="text" 
          icon={<SwapOutlined />}
          style={{ 
            color: '#1890ff',
            border: '1px solid #d9d9d9',
            borderRadius: '6px'
          }}
        >
          切换应用
        </Button>
      </Dropdown>
      
      {/* 应用状态指示器 */}
      {currentApplication && (
        <div style={{ display: 'flex', alignItems: 'center', gap: 4 }}>
          <div 
            style={{
              width: 8,
              height: 8,
              borderRadius: '50%',
              background: currentApplication.status === 1 ? '#52c41a' : '#f5222d'
            }}
          />
          <span style={{ 
            fontSize: '12px', 
            color: '#666',
            fontWeight: 'normal'
          }}>
            {currentApplication.status === 1 ? '运行中' : '已禁用'}
          </span>
        </div>
      )}
    </div>
  );
};
```

## 四、核心页面设计

### 4.1 商户层页面设计

#### 4.1.1 商户概览仪表板

##### KPI指标卡片
```jsx
<Row gutter={[16, 16]}>
  <Col xs={24} sm={12} lg={6}>
    <Card>
      <Statistic
        title="总商户数"
        value={1128}
        prefix={<UserOutlined />}
        valueStyle={{ color: '#1890ff' }}
      />
    </Card>
  </Col>
  <Col xs={24} sm={12} lg={6}>
    <Card>
      <Statistic
        title="活跃应用"
        value={93}
        suffix="个"
        prefix={<AppstoreOutlined />}
        valueStyle={{ color: '#52c41a' }}
      />
    </Card>
  </Col>
  <Col xs={24} sm={12} lg={6}>
    <Card>
      <Statistic
        title="道具总数"
        value={2456}
        prefix={<GiftOutlined />}
        valueStyle={{ color: '#faad14' }}
      />
    </Card>
  </Col>
  <Col xs={24} sm={12} lg={6}>
    <Card>
      <Statistic
        title="今日发放"
        value={11280}
        prefix={<SendOutlined />}
        valueStyle={{ color: '#f5222d' }}
      />
    </Card>
  </Col>
</Row>
```

##### 数据图表区域
```jsx
<Row gutter={[16, 16]} style={{ marginTop: 16 }}>
  <Col xs={24} lg={16}>
    <Card title="道具发放趋势" extra={<RangePicker />}>
      {/* 使用简单的统计图表，避免复杂图表库 */}
      <div style={{ height: 300 }}>
        <Empty description="图表区域" />
      </div>
    </Card>
  </Col>
  <Col xs={24} lg={8}>
    <Card title="商户活跃度">
      <List
        dataSource={merchantData}
        renderItem={item => (
          <List.Item>
            <List.Item.Meta
              avatar={<Avatar>{item.name[0]}</Avatar>}
              title={item.name}
              description={`活跃度: ${item.activity}%`}
            />
            <Progress percent={item.activity} size="small" />
          </List.Item>
        )}
      />
    </Card>
  </Col>
</Row>
```

#### 4.1.2 商户管理页面

##### 商户列表
```jsx
<Card>
  {/* 搜索和操作区 */}
  <Row gutter={16} style={{ marginBottom: 16 }}>
    <Col flex="auto">
      <Input.Search
        placeholder="搜索商户名称或ID"
        allowClear
        onSearch={handleSearch}
        style={{ width: 300 }}
      />
    </Col>
    <Col>
      <Space>
        <Select placeholder="状态筛选" style={{ width: 120 }}>
          <Option value={1}>启用</Option>
          <Option value={2}>禁用</Option>
        </Select>
        <Button type="primary" icon={<PlusOutlined />}>
          新建商户
        </Button>
      </Space>
    </Col>
  </Row>

  {/* 商户表格 */}
  <Table
    columns={merchantColumns}
    dataSource={merchantData}
    rowKey="merchant_id"
    pagination={{
      showSizeChanger: true,
      showQuickJumper: true,
      showTotal: (total) => `共 ${total} 条记录`,
    }}
  />
</Card>
```

##### 商户表格列定义
```jsx
const merchantColumns = [
  {
    title: '商户ID',
    dataIndex: 'merchant_id',
    key: 'merchant_id',
    width: 120,
  },
  {
    title: '商户名称',
    dataIndex: 'merchant_name',
    key: 'merchant_name',
  },
  {
    title: '状态',
    dataIndex: 'status',
    key: 'status',
    width: 100,
    render: (status) => (
      <Tag color={status === 1 ? 'success' : 'error'}>
        {status === 1 ? '启用' : '禁用'}
      </Tag>
    ),
  },
  {
    title: '创建时间',
    dataIndex: 'created_at',
    key: 'created_at',
    width: 180,
    render: (time) => dayjs(time).format('YYYY-MM-DD HH:mm:ss'),
  },
  {
    title: '操作',
    key: 'action',
    width: 200,
    render: (_, record) => (
      <Space>
        <Button type="link" size="small">编辑</Button>
        <Button type="link" size="small">重置密钥</Button>
        <Dropdown overlay={moreActions}>
          <Button type="link" size="small">
            更多 <DownOutlined />
          </Button>
        </Dropdown>
      </Space>
    ),
  },
];
```

### 4.2 应用层页面设计

#### 4.2.1 应用管理页面

##### 应用卡片列表
```jsx
<div>
  {/* 操作栏 */}
  <Row justify="space-between" style={{ marginBottom: 16 }}>
    <Col>
      <Space>
        <Input.Search placeholder="搜索应用" style={{ width: 300 }} />
        <Select placeholder="状态筛选" style={{ width: 120 }}>
          <Option value={1}>启用</Option>
          <Option value={2}>禁用</Option>
        </Select>
      </Space>
    </Col>
    <Col>
      <Button type="primary" icon={<PlusOutlined />}>
        创建应用
      </Button>
    </Col>
  </Row>

  {/* 应用卡片网格 */}
  <Row gutter={[16, 16]}>
    {appData.map(app => (
      <Col xs={24} sm={12} lg={8} xl={6} key={app.app_id}>
        <Card
          hoverable
          actions={[
            <EditOutlined key="edit" />,
            <SettingOutlined key="setting" />,
            <EllipsisOutlined key="ellipsis" />,
          ]}
        >
          <Card.Meta
            avatar={<Avatar size={48} icon={<AppstoreOutlined />} />}
            title={app.app_name}
            description={
              <div>
                <Tag color={app.status === 1 ? 'success' : 'error'}>
                  {app.status === 1 ? '启用' : '禁用'}
                </Tag>
                <div style={{ marginTop: 8, color: '#8c8c8c' }}>
                  ID: {app.app_id}
                </div>
              </div>
            }
          />
        </Card>
      </Col>
    ))}
  </Row>
</div>
```

### 4.3 模块层页面设计

#### 4.3.1 道具模板管理模块

##### 道具列表
```jsx
<Card>
  {/* 筛选和操作区 */}
  <Row gutter={16} style={{ marginBottom: 16 }}>
    <Col flex="auto">
      <Space>
        <Input.Search placeholder="搜索道具名称或ID" style={{ width: 300 }} />
        <Select placeholder="道具类型" style={{ width: 150 }}>
          <Option value="game_bet">游戏投注</Option>
          <Option value="lottery">抽奖券</Option>
        </Select>
        <Select placeholder="状态" style={{ width: 120 }}>
          <Option value={1}>启用</Option>
          <Option value={2}>禁用</Option>
          <Option value={3}>待删除</Option>
          <Option value={4}>过期</Option>
        </Select>
      </Space>
    </Col>
    <Col>
      <Space>
        <Button icon={<DownloadOutlined />}>导出</Button>
        <Button type="primary" icon={<PlusOutlined />}>
          创建道具
        </Button>
      </Space>
    </Col>
  </Row>

  {/* 道具表格 */}
  <Table
    columns={itemColumns}
    dataSource={itemData}
    rowKey="item_id"
    expandable={{
      expandedRowRender: (record) => (
        <Descriptions size="small" column={2}>
          <Descriptions.Item label="道具参数">
            <pre>{JSON.stringify(record.eff_arg, null, 2)}</pre>
          </Descriptions.Item>
          <Descriptions.Item label="自定义参数">
            <pre>{JSON.stringify(record.custom, null, 2)}</pre>
          </Descriptions.Item>
        </Descriptions>
      ),
    }}
    pagination={{
      showSizeChanger: true,
      showQuickJumper: true,
      showTotal: (total) => `共 ${total} 条记录`,
    }}
  />
</Card>
```

##### 道具创建/编辑表单
```jsx
<Modal
  title="创建道具模板"
  visible={visible}
  onCancel={onCancel}
  footer={null}
  width={800}
>
  <Form
    form={form}
    layout="vertical"
    onFinish={onFinish}
  >
    <Row gutter={16}>
      <Col span={12}>
        <Form.Item
          name="item_type"
          label="道具类型"
          rules={[{ required: true, message: '请输入道具类型' }]}
        >
          <Input placeholder="如：game_bet" maxLength={30} />
        </Form.Item>
      </Col>
      <Col span={12}>
        <Form.Item
          name="item_name"
          label="道具名称"
          rules={[{ required: true, message: '请输入道具名称' }]}
        >
          <Input placeholder="如：免费投注次数" maxLength={30} />
        </Form.Item>
      </Col>
    </Row>

    <Row gutter={16}>
      <Col span={12}>
        <Form.Item
          name="item_id"
          label="道具ID"
          rules={[{ required: true, message: '请输入道具ID' }]}
        >
          <Input placeholder="如：freebet10001" maxLength={30} />
        </Form.Item>
      </Col>
      <Col span={12}>
        <Form.Item name="item_icon" label="道具图标">
          <Input placeholder="图片地址" />
        </Form.Item>
      </Col>
    </Row>

    <Form.Item
      name="eff_arg"
      label="道具参数"
      rules={[{ required: true, message: '请输入道具参数' }]}
    >
      <Input.TextArea
        placeholder='{"bet_amount":0.1,"max_payout":1000}'
        rows={3}
        maxLength={200}
      />
    </Form.Item>

    <Divider>过期设置</Divider>
    <Row gutter={16}>
      <Col span={12}>
        <Form.Item name="expire_duration" label="动态过期时长(小时)">
          <InputNumber min={1} style={{ width: '100%' }} />
        </Form.Item>
      </Col>
      <Col span={12}>
        <Form.Item name="expire_date" label="固定过期时间">
          <DatePicker showTime style={{ width: '100%' }} />
        </Form.Item>
      </Col>
    </Row>

    <Divider>限制设置</Divider>
    <Row gutter={16}>
      <Col span={8}>
        <Form.Item name="limit_max" label="持有上限">
          <InputNumber min={1} style={{ width: '100%' }} />
        </Form.Item>
      </Col>
      <Col span={8}>
        <Form.Item name="daily_limit_max" label="每日发放上限">
          <InputNumber min={1} style={{ width: '100%' }} />
        </Form.Item>
      </Col>
      <Col span={8}>
        <Form.Item name="total_limit" label="总发放上限">
          <InputNumber min={1} style={{ width: '100%' }} />
        </Form.Item>
      </Col>
    </Row>

    <Form.Item name="custom" label="自定义参数">
      <Input.TextArea
        placeholder='{"currency":"all","game_code":["10001","10002"]}'
        rows={3}
        maxLength={200}
      />
    </Form.Item>

    <Form.Item>
      <Space>
        <Button type="primary" htmlType="submit">
          创建道具
        </Button>
        <Button onClick={onCancel}>
          取消
        </Button>
      </Space>
    </Form.Item>
  </Form>
</Modal>
```

#### 4.3.2 用户背包系统模块

##### 背包查询
```jsx
<Card title="用户背包查询">
  <Form layout="inline" style={{ marginBottom: 16 }}>
    <Form.Item name="player_id" label="用户ID">
      <Input placeholder="输入用户ID" style={{ width: 200 }} />
    </Form.Item>
    <Form.Item name="item_id" label="道具ID">
      <Select placeholder="选择道具" style={{ width: 200 }} allowClear>
        {itemOptions.map(item => (
          <Option key={item.value} value={item.value}>
            {item.label}
          </Option>
        ))}
      </Select>
    </Form.Item>
    <Form.Item>
      <Button type="primary" icon={<SearchOutlined />}>
        查询
      </Button>
    </Form.Item>
  </Form>

  <Table
    columns={bagColumns}
    dataSource={bagData}
    rowKey={(record) => `${record.player_id}_${record.item_id}`}
    pagination={{
      showSizeChanger: true,
      showTotal: (total) => `共 ${total} 条记录`,
    }}
  />
</Card>
```

##### 道具发放
```jsx
<Card title="道具发放">
  <Form form={form} layout="vertical" onFinish={onDistribute}>
    <Form.Item
      name="player_ids"
      label="用户ID列表"
      rules={[{ required: true, message: '请输入用户ID' }]}
    >
      <Select
        mode="tags"
        placeholder="输入用户ID，支持批量"
        style={{ width: '100%' }}
      />
    </Form.Item>

    <Form.List name="items">
      {(fields, { add, remove }) => (
        <>
          {fields.map(({ key, name, ...restField }) => (
            <Card
              key={key}
              size="small"
              title={`道具 ${key + 1}`}
              extra={
                <Button
                  type="link"
                  danger
                  onClick={() => remove(name)}
                  icon={<DeleteOutlined />}
                />
              }
              style={{ marginBottom: 16 }}
            >
              <Row gutter={16}>
                <Col span={8}>
                  <Form.Item
                    {...restField}
                    name={[name, 'item_id']}
                    label="道具ID"
                    rules={[{ required: true, message: '请选择道具' }]}
                  >
                    <Select placeholder="选择道具">
                      {itemOptions.map(item => (
                        <Option key={item.value} value={item.value}>
                          {item.label}
                        </Option>
                      ))}
                    </Select>
                  </Form.Item>
                </Col>
                <Col span={8}>
                  <Form.Item
                    {...restField}
                    name={[name, 'amount']}
                    label="发放数量"
                    rules={[{ required: true, message: '请输入数量' }]}
                  >
                    <InputNumber min={1} style={{ width: '100%' }} />
                  </Form.Item>
                </Col>
                <Col span={8}>
                  <Form.Item
                    {...restField}
                    name={[name, 'expire_time']}
                    label="过期时间"
                  >
                    <DatePicker showTime style={{ width: '100%' }} />
                  </Form.Item>
                </Col>
              </Row>
            </Card>
          ))}
          <Button
            type="dashed"
            onClick={() => add()}
            block
            icon={<PlusOutlined />}
          >
            添加道具
          </Button>
        </>
      )}
    </Form.List>

    <Form.Item name="remark" label="备注">
      <Input.TextArea placeholder="发放事由说明" rows={3} />
    </Form.Item>

    <Form.Item>
      <Button type="primary" htmlType="submit" size="large">
        确认发放
      </Button>
    </Form.Item>
  </Form>
</Card>
```

#### 4.3.3 数据统计功能模块

##### 统计概览
```jsx
<div>
  {/* 时间筛选 */}
  <Card size="small" style={{ marginBottom: 16 }}>
    <Row gutter={16} align="middle">
      <Col>
        <span>时间范围：</span>
      </Col>
      <Col>
        <RangePicker />
      </Col>
      <Col>
        <Select placeholder="商户筛选" style={{ width: 200 }}>
          <Option value="all">全部商户</Option>
          {merchantOptions.map(merchant => (
            <Option key={merchant.value} value={merchant.value}>
              {merchant.label}
            </Option>
          ))}
        </Select>
      </Col>
      <Col>
        <Button type="primary">查询</Button>
      </Col>
    </Row>
  </Card>

  {/* 统计卡片 */}
  <Row gutter={[16, 16]}>
    <Col xs={24} sm={12} lg={6}>
      <Card>
        <Statistic
          title="总发放量"
          value={112893}
          prefix={<SendOutlined />}
          valueStyle={{ color: '#1890ff' }}
        />
      </Card>
    </Col>
    <Col xs={24} sm={12} lg={6}>
      <Card>
        <Statistic
          title="总消耗量"
          value={98765}
          prefix={<ConsumeOutlined />}
          valueStyle={{ color: '#52c41a' }}
        />
      </Card>
    </Col>
    <Col xs={24} sm={12} lg={6}>
      <Card>
        <Statistic
          title="剩余库存"
          value={14128}
          prefix={<InboxOutlined />}
          valueStyle={{ color: '#faad14' }}
        />
      </Card>
    </Col>
    <Col xs={24} sm={12} lg={6}>
      <Card>
        <Statistic
          title="活跃用户"
          value={5432}
          prefix={<UserOutlined />}
          valueStyle={{ color: '#f5222d' }}
        />
      </Card>
    </Col>
  </Row>

  {/* 详细统计表格 */}
  <Card title="道具统计详情" style={{ marginTop: 16 }}>
    <Table
      columns={statisticsColumns}
      dataSource={statisticsData}
      rowKey="item_id"
      pagination={{
        showSizeChanger: true,
        showTotal: (total) => `共 ${total} 条记录`,
      }}
    />
  </Card>
</div>
```

## 五、通用组件设计

### 5.1 状态标签组件
```jsx
const StatusTag = ({ status, type = 'item' }) => {
  const getConfig = () => {
    if (type === 'item') {
      const configs = {
        1: { color: 'success', text: '启用' },
        2: { color: 'error', text: '禁用' },
        3: { color: 'warning', text: '待删除' },
        4: { color: 'default', text: '过期' },
        5: { color: 'default', text: '已删除' },
      };
      return configs[status] || { color: 'default', text: '未知' };
    }
    // 其他类型的状态配置...
  };

  const config = getConfig();
  return <Tag color={config.color}>{config.text}</Tag>;
};
```

### 5.2 操作确认组件
```jsx
const ConfirmAction = ({ title, content, onConfirm, children }) => {
  return (
    <Popconfirm
      title={title}
      description={content}
      onConfirm={onConfirm}
      okText="确认"
      cancelText="取消"
    >
      {children}
    </Popconfirm>
  );
};
```

### 5.3 数据导出组件
```jsx
const ExportButton = ({ data, filename, ...props }) => {
  const handleExport = () => {
    // 导出逻辑
    message.success('导出成功');
  };

  return (
    <Button
      icon={<DownloadOutlined />}
      onClick={handleExport}
      {...props}
    >
      导出数据
    </Button>
  );
};
```

## 六、响应式设计实现

### 6.1 栅格系统使用
```jsx
// 响应式列配置
<Row gutter={[16, 16]}>
  <Col xs={24} sm={12} md={8} lg={6} xl={4}>
    <Card>内容</Card>
  </Col>
</Row>

// 响应式表格
<Table
  columns={columns}
  dataSource={data}
  scroll={{ x: 800 }} // 横向滚动
  pagination={{
    responsive: true,
    showSizeChanger: true,
  }}
/>
```

### 6.2 移动端适配
```jsx
// 移动端抽屉导航
<Drawer
  title="导航菜单"
  placement="left"
  visible={mobileMenuVisible}
  onClose={() => setMobileMenuVisible(false)}
  bodyStyle={{ padding: 0 }}
>
  <Menu mode="inline" />
</Drawer>

// 移动端卡片列表
<List
  grid={{
    gutter: 16,
    xs: 1,
    sm: 2,
    md: 3,
    lg: 4,
  }}
  dataSource={data}
  renderItem={item => (
    <List.Item>
      <Card>{item.content}</Card>
    </List.Item>
  )}
/>
```

## 七、交互设计规范

### 7.1 加载状态
```jsx
// 页面加载
<Spin spinning={loading} tip="加载中...">
  <div>{content}</div>
</Spin>

// 按钮加载
<Button loading={submitting} type="primary">
  提交
</Button>

// 表格加载
<Table loading={loading} />
```

### 7.2 消息提示
```jsx
// 成功提示
message.success('操作成功');

// 错误提示
message.error('操作失败，请重试');

// 警告提示
message.warning('请注意数据安全');

// 通知提醒
notification.info({
  message: '系统通知',
  description: '您有新的消息',
});
```

### 7.3 表单验证
```jsx
const rules = {
  required: [{ required: true, message: '此字段为必填项' }],
  email: [
    { required: true, message: '请输入邮箱' },
    { type: 'email', message: '邮箱格式不正确' },
  ],
  phone: [
    { required: true, message: '请输入手机号' },
    { pattern: /^1[3-9]\d{9}$/, message: '手机号格式不正确' },
  ],
};
```

## 八、性能优化建议

### 8.1 组件懒加载
```jsx
import { lazy, Suspense } from 'react';

const LazyComponent = lazy(() => import('./Component'));

<Suspense fallback={<Spin />}>
  <LazyComponent />
</Suspense>
```

### 8.2 表格虚拟滚动
```jsx
<Table
  virtual
  scroll={{ y: 400 }}
  pagination={false}
/>
```

### 8.3 图片懒加载
```jsx
<Image
  src={imageUrl}
  placeholder={<Spin />}
  fallback="data:image/png;base64,..."
/>
```

## 九、开发规范

### 9.1 组件命名
- 使用 PascalCase 命名组件
- 文件名与组件名保持一致
- 使用描述性的命名

### 9.2 样式规范
- 优先使用 Ant Design 的内置样式
- 自定义样式使用 CSS Modules 或 styled-components
- 避免内联样式，除非是动态样式

### 9.3 代码组织（Next.js架构）
```
项目根目录/
├── app/                # Next.js App Router目录
│   ├── (dashboard)/    # 路由组
│   │   ├── merchant/   # 商户管理页面
│   │   ├── application/ # 应用管理页面
│   │   └── modules/    # 模块功能页面
│   ├── globals.css     # 全局样式
│   ├── layout.tsx      # 根布局组件
│   └── page.tsx        # 首页
├── components/         # 通用组件
│   ├── ui/            # 基础UI组件
│   ├── layout/        # 布局组件
│   └── business/      # 业务组件
├── hooks/             # 自定义 Hooks
├── lib/               # 工具函数和配置
├── services/          # API 服务
├── types/             # TypeScript类型定义
├── constants/         # 常量定义
└── public/            # 静态资源
```

这个设计方案严格基于 Ant Design 组件库，保持界面简洁专业，完全匹配 PRD 需求范围，为后续的前端开发提供了详细的实现指导。