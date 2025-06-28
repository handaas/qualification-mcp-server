# 招投标大数据服务

[该MCP服务提供全面的企业资质证书信息查询功能，包括荣誉资质、企业资质、行政许可、资质证书概况等，帮助用户了解企业的资质背景和专业能力。](https://www.handaas.com/)


## 主要功能

- 🔍 企业关键词模糊搜索
- 🏆 荣誉资质查询
- 📋 企业资质信息查询
- 📜 行政许可信息查询
- 📊 资质证书概况统计

## 环境要求

- Python 3.10+
- 依赖包：python-dotenv, requests, mcp

## 本地快速启动

### 1. 克隆项目
```bash
git clone https://github.com/handaas/qualification-mcp-server
cd qualification-mcp-server
```

### 2. 创建虚拟环境&安装依赖

```bash
python -m venv mcp_env && source mcp_env/bin/activate
pip install -r requirements.txt
```

### 3. 环境配置

复制环境变量模板并配置：

```bash
cp .env.example .env
```

编辑 `.env` 文件，配置以下环境变量：

```env
INTEGRATOR_ID=your_integrator_id
SECRET_ID=your_secret_id
SECRET_KEY=your_secret_key
```

### 4. streamable-http启动服务

```bash
python server/mcp_server.py streamable-http
```

服务将在 `http://localhost:8000` 启动。

#### 支持启动方式 stdio 或 sse 或 streamable-http

### 5. Cursor / Cherry Studio MCP配置

```json
{
  "mcpServers": {
    "handaas-mcp-server": {
      "type": "streamableHttp",
      "url": "http://127.0.0.1:8000/mcp"
    }
  }
}
```

## STDIO版安装部署

### 设置Cursor / Cherry Studio MCP配置

```json
{
  "mcpServers": {
    "qualification-mcp-server": {
      "command": "uv",
      "args": ["run", "mcp", "run", "{workdir}/server/mcp_server.py"],
      "env": {
        "PATH": "{workdir}/mcp_env/bin:$PATH",
        "PYTHONPATH": "{workdir}/mcp_env",
        "INTEGRATOR_ID": "your_integrator_id",
        "SECRET_ID": "your_secret_id",
        "SECRET_KEY": "your_secret_key"
      }
    }
  }
}
```

## 使用官方Remote服务

### 1. 直接设置Cursor / Cherry Studio MCP配置

```json
{
  "mcpServers": {
    "qualification-mcp-server":{
      "type": "streamableHttp",
      "url": "https://mcp.handaas.com/bidding/bidding_bigdata?token={token}"  
      }
  }
}
```

### 注意：integrator_id、secret_id、secret_key及token需要登录 https://www.handaas.com/ 进行注册开通平台获取


## 可用工具

### 1. qualification_bigdata_fuzzy_search
**功能**: 企业关键词模糊查询

根据提供的企业名称、人名、品牌、产品、岗位等关键词模糊查询相关企业列表。

**参数**:
- `matchKeyword` (必需): 匹配关键词
- `pageIndex` (可选): 分页开始位置
- `pageSize` (可选): 分页结束位置

**返回值**:
- `total`: 总数
- `resultList`: 结果列表

### 2. qualification_bigdata_honor_qualifications
**功能**: 荣誉资质查询

根据输入的企业相关信息，查询并返回企业获得的荣誉资质详细信息，包括认定机关、资质级别、有效期等。

**参数**:
- `matchKeyword` (必需): 企业名称/注册号/统一社会信用代码/企业id
- `keywordType` (可选): 主体类型枚举

**返回值**:
- `total`: 总数
- `resultList`: 荣誉资质列表
  - `eaqBeginDate`: 有效期开始日期
  - `eaqPublishDate`: 发布日期
  - `eaqRecognitionLevel`: 资质级别
  - `eaqTitle`: 公告标题
  - `eaqEndDate`: 有效期截止日期
  - `eaqType`: 资质类型
  - `eaqAuthority`: 认定机关

### 3. qualification_bigdata_enterprise_qualifications
**功能**: 企业资质信息查询

查询和返回企业的资质信息，包括资质总数、资质信息列表、企业资质分类、资质类型等多维度数据。

**参数**:
- `matchKeyword` (必需): 企业名称/注册号/统一社会信用代码/企业id
- `keywordType` (可选): 主体类型枚举
- `pageIndex` (可选): 页码
- `pageSize` (可选): 分页大小 - 一页最多获取10条数据

**返回值**:
- `total`: 资质总数
- `resultList`: 资质信息列表
  - `qualificationClasses`: 企业资质分类（1：企业创新与成长类，2：科技研发与创新类，3：科技服务与孵化类，4：知识产权类，5：产业升级与转型类）
  - `qualificationType`: 资质类型
  - `publishYear`: 发布年份
  - `publishDate`: 发布日期
  - `qualificationLevel`: 资质级别
  - `agency`: 发布单位
  - `highTechIndustryList`: 高新行业分类
  - `greenProducts`: 绿色设计产品

### 4. qualification_bigdata_administrative_licenses
**功能**: 行政许可信息查询

根据提供的企业标识信息查询并返回企业的相关行政许可信息，包括许可详情和有效期限。

**参数**:
- `matchKeyword` (必需): 企业名称/注册号/统一社会信用代码/企业id
- `pageSize` (可选): 分页大小 - 一页最多获取50条数据
- `pageIndex` (可选): 页码
- `keywordType` (可选): 主体类型枚举

**返回值**:
- `total`: 总数
- `resultList`: 行政许可列表
  - `authority`: 许可机关
  - `auditType`: 审核类型
  - `beginDate`: 许可有效期自
  - `content`: 许可内容
  - `status`: 当前状态
  - `endDate`: 许可有效期至
  - `creditPubLicenseId`: 行政许可决定文书号
  - `fileName`: 行政许可决定文书名称

### 5. qualification_bigdata_qualification_certificate_profile
**功能**: 资质证书概况统计

根据企业的基本信息查询该企业的资质证书相关概况，包括总证书数量、获证时间分析等。

**参数**:
- `matchKeyword` (必需): 企业名称/注册号/统一社会信用代码/企业id
- `keywordType` (可选): 主体类型枚举

**返回值**:
- `certificateCount`: 证书数量
- `certNumberThisYear`: 最近一年获证数量
- `certFirstPublishTime`: 初次获证日期
- `certTypeList`: 涵盖证书类别
- `certLastPublishTime`: 最近获证日期

## 使用场景

1. **资质验证**: 企业、投资者、合作伙伴或监管机构快速获取企业资质背景
2. **招投标审核**: 企业在参与项目投标时，验证资质是否符合要求
3. **合作评估**: 合作伙伴核实企业的资质真实性，评估合作风险
4. **合规检查**: 政府部门进行企业合规性检查和行业资质监管
5. **金融审批**: 金融机构在贷款审批时进行背景调查
6. **市场调研**: 了解行业内企业的资质分布和趋势

## 使用注意事项

1. **企业全称要求**: 在调用需要企业全称的接口时，如果没有企业全称则先调取qualification_bigdata_fuzzy_search接口获取企业全称
2. **分页限制**: 不同接口有不同的分页限制，企业资质查询一页最多10条，行政许可查询一页最多50条
3. **有效期关注**: 关注资质和许可的有效期，确保资质的时效性
4. **资质分类**: 企业资质分为5个主要类别，便于分类管理和查询
5. **数据时效性**: 资质信息会定期更新，建议关注最新的获证日期

## 使用提问示例

### qualification_bigdata_honor_qualifications (荣誉资质查询)
3. 腾讯获得了哪些荣誉资质？认定机关都是哪些？
4. 阿里巴巴的荣誉资质级别都是什么？有效期到什么时候？
5. 华为有多少个荣誉资质？主要是什么类型的？

### qualification_bigdata_enterprise_qualifications (企业资质信息查询)
6. 百度的企业资质总共有多少个？主要分布在哪些类别？
7. 腾讯的科技研发类资质有哪些？是什么级别的？
8. 抖音的高新技术企业资质情况如何？

### qualification_bigdata_administrative_licenses (行政许可信息查询)
9. 阿里巴巴有哪些行政许可？许可机关是谁？
10. 华为的行政许可都有效期到什么时候？
11. 腾讯的行政许可决定文书都有哪些？

### qualification_bigdata_qualification_certificate_profile (资质证书概况统计)
12. 百度总共有多少张资质证书？最近一年新增了多少？
13. 阿里巴巴初次获证是什么时候？涵盖哪些证书类别？
14. 华为最近获证日期是什么时候？证书数量趋势如何？