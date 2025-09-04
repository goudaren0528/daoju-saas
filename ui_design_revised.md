# 道具SaaS平台UI设计方案（PRD严格对照版）

## 版本信息
- 版本：1.1.0
- 创建时间：2025-09-04
- 修订说明：严格对照PRD需求，移除过度设计元素
- 状态：已修正

## 一、PRD需求对照分析

### 1.1 PRD明确定义的功能范围
根据PRD文档，道具SaaS平台的核心功能严格限定为：

#### SaaS平台实现能力（PRD第四章节）
1. **商户管理**
   - 商户创建、更新、状态管理
   - 密钥重置功能

2. **应用管理**
   - 应用创建、状态修改（启用/禁用）
   - 应用查询（不支持删除）

3. **道具模板管理**
   - 道具模板创建、修改、删除、查询
   - 道具模板用量库存统计

4. **用户背包操作**
   - 用户背包道具的发放、查询、修改
   - 用户道具流水记录

### 1.2 设计方案中需要移除的过度设计
经对比分析，以下功能**不在PRD范围内**，需要从UI设计中移除：

❌ **过度设计项目**：
- 概览仪表板的复杂图表（PRD未提及仪表板需求）
- 实时监控面板（PRD未提及实时监控）
- 商户统计分析（PRD仅要求基础统计）
- 权限管理系统（PRD未提及权限功能）
- 审计日志（PRD标注"暂未提供"）
- API接口管理界面（PRD未要求界面管理API）
- 订阅计费界面（PRD未提及计费功能）
- 多维度数据分析（超出PRD统计范围）
- 报表中心（PRD仅要求数据导出）

## 二、修正后的功能模块设计

### 2.1 严格按PRD的功能模块
```
🏢 商户管理
├── 商户列表（查看、状态管理）
└── 密钥重置

📱 应用管理  
├── 应用列表
├── 创建应用
└── 应用状态管理

🎁 道具模板管理
├── 道具模板列表
├── 创建道具模板
├── 编辑道具模板
├── 删除道具模板（软删除）
└── 道具库存统计

👤 用户背包管理
├── 道具发放
├── 用户道具查询
├── 道具修改
├── 道具消耗
└── 道具流水记录查询

📊 基础统计
└── 道具产耗统计（发放量、消耗量、库存）
```

### 2.2 简化的导航结构
```
道具SaaS管理平台
├── 商户管理
├── 应用管理  
├── 道具模板
├── 用户背包
└── 统计查询
```

## 三、核心页面设计（严格按PRD）

### 3.1 商户管理页面

#### 3.1.1 商户列表
**功能对照PRD**：商户信息查看、状态管理、密钥重置

```jsx
<Card title="商户管理">
  {/* 搜索区域 - 简化设计 */}
  <Row style={{ marginBottom: 16 }}>
    <Col>
      <Input.Search 
        placeholder="搜索商户ID或名称" 
        style={{ width: 300 }} 
      />
    </Col>
  </Row>

  {/* 商户表格 - 仅显示PRD定义字段 */}
  <Table
    columns={[
      {
        title: '商户ID',
        dataIndex: 'merchant_id',
        key: 'merchant_id',
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
        render: (status) => (
          <Tag color={status === 1 ? 'success' : 'error'}>
            {status === 1 ? '启用' : '禁用'}
          </Tag>
        ),
      },
      {
        title: '操作',
        key: 'action',
        render: (_, record) => (
          <Space>
            <Button type="link" size="small">
              {record.status === 1 ? '禁用' : '启用'}
            </Button>
            <Button type="link" size="small">
              重置密钥
            </Button>
          </Space>
        ),
      },
    ]}
    dataSource={merchantData}
    pagination={{ showSizeChanger: true }}
  />
</Card>
```

### 3.2 应用管理页面

#### 3.2.1 应用列表（PRD：创建、状态修改、查询）
```jsx
<Card title="应用管理">
  <Row justify="space-between" style={{ marginBottom: 16 }}>
    <Col>
      <Input.Search placeholder="搜索应用名称" style={{ width: 300 }} />
    </Col>
    <Col>
      <Button type="primary" icon={<PlusOutlined />}>
        创建应用
      </Button>
    </Col>
  </Row>

  <Table
    columns={[
      {
        title: '应用ID',
        dataIndex: 'app_id',
        key: 'app_id',
      },
      {
        title: '应用名称',
        dataIndex: 'app_name', 
        key: 'app_name',
      },
      {
        title: '状态',
        dataIndex: 'status',
        key: 'status',
        render: (status) => (
          <Tag color={status === 1 ? 'success' : 'error'}>
            {status === 1 ? '启用' : '禁用'}
          </Tag>
        ),
      },
      {
        title: '操作',
        key: 'action',
        render: (_, record) => (
          <Button type="link" size="small">
            {record.status === 1 ? '禁用' : '启用'}
          </Button>
        ),
      },
    ]}
    dataSource={appData}
  />
</Card>
```

### 3.3 道具模板管理页面

#### 3.3.1 道具模板列表（PRD完整字段）
```jsx
<Card title="道具模板管理">
  <Row justify="space-between" style={{ marginBottom: 16 }}>
    <Col>
      <Space>
        <Input.Search placeholder="搜索道具ID或名称" style={{ width: 300 }} />
        <Select placeholder="状态筛选" style={{ width: 120 }}>
          <Option value={1}>启用</Option>
          <Option value={2}>禁用</Option>
          <Option value={3}>待删除</Option>
          <Option value={4}>过期</Option>
        </Select>
      </Space>
    </Col>
    <Col>
      <Button type="primary" icon={<PlusOutlined />}>
        创建道具模板
      </Button>
    </Col>
  </Row>

  <Table
    columns={[
      { title: '道具ID', dataIndex: 'item_id', key: 'item_id' },
      { title: '道具名称', dataIndex: 'item_name', key: 'item_name' },
      { title: '道具类型', dataIndex: 'item_type', key: 'item_type' },
      {
        title: '状态',
        dataIndex: 'status',
        key: 'status',
        render: (status) => {
          const statusMap = {
            1: { color: 'success', text: '启用' },
            2: { color: 'error', text: '禁用' },
            3: { color: 'warning', text: '待删除' },
            4: { color: 'default', text: '过期' },
            5: { color: 'default', text: '已删除' },
          };
          const config = statusMap[status];
          return <Tag color={config.color}>{config.text}</Tag>;
        },
      },
      {
        title: '操作',
        key: 'action',
        render: (_, record) => (
          <Space>
            <Button type="link" size="small">编辑</Button>
            <Button type="link" size="small" danger>删除</Button>
          </Space>
        ),
      },
    ]}
    dataSource={itemData}
    expandable={{
      expandedRowRender: (record) => (
        <Descriptions size="small" column={2}>
          <Descriptions.Item label="道具参数">
            {JSON.stringify(record.eff_arg)}
          </Descriptions.Item>
          <Descriptions.Item label="过期时长">
            {record.expire_duration ? `${record.expire_duration}小时` : '无限制'}
          </Descriptions.Item>
          <Descriptions.Item label="固定过期时间">
            {record.expire_date || '无限制'}
          </Descriptions.Item>
          <Descriptions.Item label="持有上限">
            {record.limit_max || '无限制'}
          </Descriptions.Item>
          <Descriptions.Item label="每日发放上限">
            {record.daily_limit_max || '无限制'}
          </Descriptions.Item>
          <Descriptions.Item label="总发放上限">
            {record.total_limit || '无限制'}
          </Descriptions.Item>
        </Descriptions>
      ),
    }}
  />
</Card>
```

#### 3.3.2 道具模板创建表单（严格按PRD参数）
```jsx
<Modal title="创建道具模板" visible={visible} onCancel={onCancel} footer={null} width={800}>
  <Form form={form} layout="vertical" onFinish={onFinish}>
    {/* 基础信息 - PRD必填字段 */}
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
          <Input placeholder="图片地址（可选）" />
        </Form.Item>
      </Col>
    </Row>

    {/* 道具参数 - PRD必填 */}
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

    {/* 过期设置 - PRD可选字段 */}
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

    {/* 限制设置 - PRD可选字段 */}
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

    {/* 自定义参数 - PRD可选字段 */}
    <Form.Item name="custom" label="自定义参数">
      <Input.TextArea
        placeholder='{"currency":"all","game_code":["10001","10002"]}'
        rows={3}
        maxLength={200}
      />
    </Form.Item>

    <Form.Item>
      <Space>
        <Button type="primary" htmlType="submit">创建道具</Button>
        <Button onClick={onCancel}>取消</Button>
      </Space>
    </Form.Item>
  </Form>
</Modal>
```

### 3.4 用户背包管理页面

#### 3.4.1 道具发放（PRD功能）
```jsx
<Card title="道具发放">
  <Form form={form} layout="vertical" onFinish={onDistribute}>
    <Form.Item
      name="player_ids"
      label="用户ID"
      rules={[{ required: true, message: '请输入用户ID' }]}
    >
      <Select
        mode="tags"
        placeholder="输入用户ID，支持批量"
        style={{ width: '100%' }}
      />
    </Form.Item>

    <Form.Item
      name="app_id"
      label="应用ID"
      rules={[{ required: true, message: '请选择应用' }]}
    >
      <Select placeholder="选择应用">
        {appOptions.map(app => (
          <Option key={app.app_id} value={app.app_id}>
            {app.app_name}
          </Option>
        ))}
      </Select>
    </Form.Item>

    <Form.List name="items">
      {(fields, { add, remove }) => (
        <>
          {fields.map(({ key, name, ...restField }) => (
            <Card key={key} size="small" title={`道具 ${key + 1}`}>
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
                        <Option key={item.item_id} value={item.item_id}>
                          {item.item_name}
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
              <Button type="link" danger onClick={() => remove(name)}>
                删除此道具
              </Button>
            </Card>
          ))}
          <Button type="dashed" onClick={() => add()} block>
            添加道具
          </Button>
        </>
      )}
    </Form.List>

    <Form.Item name="remark" label="备注">
      <Input.TextArea placeholder="发放事由说明" rows={2} />
    </Form.Item>

    <Form.Item>
      <Button type="primary" htmlType="submit">确认发放</Button>
    </Form.Item>
  </Form>
</Card>
```

#### 3.4.2 用户道具查询（PRD功能）
```jsx
<Card title="用户道具查询">
  <Form layout="inline" style={{ marginBottom: 16 }}>
    <Form.Item name="player_id" label="用户ID">
      <Input placeholder="输入用户ID" style={{ width: 200 }} />
    </Form.Item>
    <Form.Item name="app_id" label="应用ID">
      <Select placeholder="选择应用" style={{ width: 200 }}>
        {appOptions.map(app => (
          <Option key={app.app_id} value={app.app_id}>
            {app.app_name}
          </Option>
        ))}
      </Select>
    </Form.Item>
    <Form.Item>
      <Button type="primary">查询</Button>
    </Form.Item>
  </Form>

  <Table
    columns={[
      { title: '用户ID', dataIndex: 'player_id', key: 'player_id' },
      { title: '道具ID', dataIndex: 'item_id', key: 'item_id' },
      { title: '道具数量', dataIndex: 'amount', key: 'amount' },
      { 
        title: '过期时间', 
        dataIndex: 'expire_time', 
        key: 'expire_time',
        render: (time) => time ? dayjs(time).format('YYYY-MM-DD HH:mm:ss') : '-'
      },
      { 
        title: '获取时间', 
        dataIndex: 'obtain_time', 
        key: 'obtain_time',
        render: (time) => dayjs(time).format('YYYY-MM-DD HH:mm:ss')
      },
      {
        title: '状态',
        dataIndex: 'status',
        key: 'status',
        render: (status) => (
          <Tag color={status === 1 ? 'success' : 'error'}>
            {status === 1 ? '正常' : '过期'}
          </Tag>
        ),
      },
      {
        title: '操作',
        key: 'action',
        render: (_, record) => (
          <Space>
            <Button type="link" size="small">修改</Button>
            <Button type="link" size="small">消耗</Button>
          </Space>
        ),
      },
    ]}
    dataSource={bagData}
    pagination={{ showSizeChanger: true }}
  />
</Card>
```

### 3.5 基础统计页面（仅PRD要求的统计）

#### 3.5.1 道具产耗统计（PRD明确功能）
```jsx
<Card title="道具产耗统计">
  <Form layout="inline" style={{ marginBottom: 16 }}>
    <Form.Item name="app_id" label="应用ID">
      <Select placeholder="选择应用" style={{ width: 200 }}>
        {appOptions.map(app => (
          <Option key={app.app_id} value={app.app_id}>
            {app.app_name}
          </Option>
        ))}
      </Select>
    </Form.Item>
    <Form.Item name="item_ids" label="道具ID">
      <Select mode="multiple" placeholder="选择道具" style={{ width: 300 }}>
        {itemOptions.map(item => (
          <Option key={item.item_id} value={item.item_id}>
            {item.item_name}
          </Option>
        ))}
      </Select>
    </Form.Item>
    <Form.Item>
      <Button type="primary">查询统计</Button>
    </Form.Item>
  </Form>

  <Table
    columns={[
      { title: '道具ID', dataIndex: 'item_id', key: 'item_id' },
      { title: '已发放量', dataIndex: 'distributed', key: 'distributed' },
      { title: '消耗量', dataIndex: 'usage', key: 'usage' },
      { 
        title: '库存', 
        dataIndex: 'remaining', 
        key: 'remaining',
        render: (remaining) => remaining === -1 ? '无限制' : remaining
      },
    ]}
    dataSource={statisticsData}
  />
</Card>
```

## 四、数据导出功能（PRD要求）

### 4.1 数据导出组件
```jsx
const ExportButton = ({ exportType, filters }) => {
  const handleExport = (format) => {
    // 根据PRD要求支持csv/json格式
    const params = {
      ...filters,
      format, // 'csv' | 'json'
      export_type: exportType, // 'items' | 'bag_records'
    };
    
    // 调用导出API
    exportData(params).then(() => {
      message.success(`${format.toUpperCase()}格式数据导出成功`);
    });
  };

  return (
    <Dropdown
      overlay={
        <Menu>
          <Menu.Item key="csv" onClick={() => handleExport('csv')}>
            导出CSV
          </Menu.Item>
          <Menu.Item key="json" onClick={() => handleExport('json')}>
            导出JSON
          </Menu.Item>
        </Menu>
      }
    >
      <Button icon={<DownloadOutlined />}>
        导出数据 <DownOutlined />
      </Button>
    </Dropdown>
  );
};
```

## 五、设计原则总结

### 5.1 严格遵循PRD原则
1. **功能范围严格限制**：仅实现PRD明确定义的功能
2. **字段完整对应**：所有表单和表格字段与PRD参数一一对应
3. **业务流程准确**：操作流程严格按照PRD业务场景设计
4. **状态管理精确**：状态值和含义与PRD完全一致

### 5.2 简洁性原则
1. **界面元素最小化**：移除所有装饰性和非必要元素
2. **操作路径直接**：每个功能都有最直接的操作路径
3. **信息展示清晰**：重要信息突出，次要信息收起

### 5.3 SaaS标准规范
1. **多租户数据隔离**：通过merchant_id和app_id确保数据隔离
2. **批量操作支持**：支持PRD要求的批量处理功能
3. **错误处理完整**：覆盖PRD定义的所有错误场景
4. **数据导出标准**：按PRD要求支持csv/json格式导出

## 六、与原设计方案的主要差异

### 6.1 移除的过度设计
- ❌ 删除概览仪表板的复杂图表
- ❌ 删除实时监控功能
- ❌ 删除权限管理模块
- ❌ 删除审计日志功能
- ❌ 删除API管理界面
- ❌ 删除订阅计费功能
- ❌ 删除高级数据分析功能

### 6.2 保留的核心功能
- ✅ 商户管理（状态、密钥重置）
- ✅ 应用管理（创建、状态管理）
- ✅ 道具模板管理（完整CRUD）
- ✅ 用户背包操作（发放、查询、修改、消耗）
- ✅ 基础统计（产耗统计）
- ✅ 数据导出（csv/json）

### 6.3 设计优化
- 简化导航结构，减少层级
- 优化表单设计，突出必填字段
- 统一状态展示，与PRD状态值对应
- 简化操作流程，提高效率

这个修正版的UI设计方案严格按照PRD需求范围，移除了所有过度设计元素，确保每个界面元素都有明确的业务目标支撑，符合SaaS行业的简洁专业标准。