 # 校园助手（Campus Assistant）

 一个基于 Web 的前后端分离校园综合服务平台，前端采用 Vue 3，后端采用 Spring Boot 3。项目聚焦“校园场景的深度智能化”，在常见信息聚合功能之外，重点打造“校内专属知识与工具增强的 AI 助手”，并支持温湿度传感器等 IoT 接入，扩展到导航小车等高阶硬件，以满足毕业设计对工作量、创新性与可落地性的要求。

 ---

 ## 1. 项目定位与目标
 - **定位**：统一入口的校园数字化助手，提供信息获取、事务办理、互动交流与智能问答/任务编排。
 - **用户角色**：学生、教师、管理员；后续可扩展访客/社团。
 - **核心价值**：
   - 将“通用大模型”变为“校园场景专家”：接入校内知识库、工具与日历/导航/论坛等系统，实现有据可依、可落地执行的智能服务。
   - 兼顾可用性与可实现性：在一个学期范围内完成 MVP 并具备可展示的差异化亮点与工程深度。

 ---

 ## 2. 特色亮点（与直接使用外部大模型的差异化）
 - **校园专属知识库 RAG（检索增强生成）**：
   - 索引校内规章、流程、学院通知、食堂/教室/社团等结构化与非结构化文档；
   - 返回答案附带“引用来源/citation”，默认可展开“依据原文”。
 - **工具调用与任务编排（Agent + Tools）**：
   - 统一工具层：课表查询、论坛搜索与发帖、教室预约、校园地图路径规划、设备数据获取、天气与传感器融合等；
   - 多步任务（如“帮我规划今天课程空档去图书馆取书并顺路去二食堂”）由 AI 自动分解、调用工具并生成可执行计划。
 - **个性化与隐私**：
   - 本地优先（Ollama）+ 云端增强（OpenAI/Azure/通义/文心等）双通道策略；
   - 敏感字段最小化上云、可选择完全离线模式（仅本地 LLM + 校园知识库）。
 - **成本与质量控制**：
   - 命中率缓存、提示模板管理、意图分类/拒识（不走大模型）、召回/重排策略评估面板。
 - **可视化评测与 A/B**：
   - 针对校园 QA 集合的离线评测（BLEU/ROUGE/召回率/引用覆盖率）+ 人工打分。

 ---

 ## 3. 系统架构与技术选型
 - **前端（Web/PWA）**：
   - Vue 3 + Vite + TypeScript + Pinia + Vue Router
   - UI：Element Plus 或 Naive UI；地图：高德/Mapbox GL；可选 TailwindCSS
   - 实时：WebSocket（通知/论坛动态/设备告警）
   - PWA：离线缓存、消息推送（可选）
 - **后端（核心）**：
   - Spring Boot 3（Java 17）、Spring Web、Spring Validation、Spring Security（JWT）、
     Spring Data JPA 或 MyBatis、Spring WebSocket
   - 数据库：PostgreSQL（推荐）或 MySQL；向量检索：PostgreSQL + pgvector（推荐）
   - 缓存：Redis；对象存储：MinIO（图片/附件/头像）
   - 搜索（可选）：Elasticsearch/OpenSearch（论坛与公告检索）
   - 消息与 IoT：MQTT（EMQX/Mosquitto）
 - **AI 子系统**：
   - Java 侧优先：LangChain4j + OpenAI/Azure/通义/Qwen/Ollama（本地）
   - 向量化：text-embedding（OpenAI/讯飞/本地嵌入）→ pgvector 存储
   - 检索与重排：BM25（可选 ES）+ 向量召回 + 语义重排
   - 工具层：统一工具注册与权限控制（教务/论坛/导航/IoT）
 - **硬件/IoT**：
   - 传感器：ESP32 + DHT22/AM2302（温湿度）→ MQTT → 后端 → 时序存储（PostgreSQL + TimescaleDB 可选）
   - 导航小车（可选扩展，高工作量项）：ROS 2 + RPi4/Jetson Nano + LiDAR/IMU；后端通过 REST/gRPC 下发路径、订阅状态
 - **部署**：
   - 开发：Docker Compose（postgres、redis、minio、emqx、ollama 可选）
   - 生产：Docker Compose 或 K8s；日志（ELK/EFK）、监控（Prometheus+Grafana）、错误跟踪（Sentry）

 > 参考分层：前端（Web/PWA）↔ API 网关/鉴权 ↔ 业务服务（教务/论坛/导航/IoT）↔ AI 服务（RAG/工具编排）↔ 基础设施（DB/Redis/MQTT/存储）

 ---

 ## 4. 功能模块
 - **AI 助手**：对话、知识库检索、工具调用、计划编排、引用来源、会话记忆（按用户隔离）。
 - **论坛与点评**：发帖/评论/点赞/举报、课程与食堂/宿舍/服务点评、标签与搜索、话题热榜、内容审核。
 - **校园导航**：校内地图、建筑/教室检索、无障碍路径、到达时间估计、（可选）室内导航/AR。
 - **课表/教务（可插拔）**：本地维护/导入课表，提醒/冲突检测；后续可接校内统一认证与 API。
 - **通知公告/活动**：学院/学校公告聚合、活动报名与提醒、订阅机制。
 - **物联网**：
   - 温湿度看板：传感器数据实时展示、历史曲线、异常阈值告警；
   - 设备管理：设备注册、健康状态、固件/密钥管理（基础版）。
 - **（可选）导航小车**：演示级任务——在指定区域内巡航、避障与路径跟随，支持后端任务下发与遥控。
 - **通用**：用户中心、RBAC 权限、站内通知、文件上传、搜索。

 ---

 ## 5. 数据库与数据模型概览（简要）
 - 用户与权限：users、roles、user_roles、api_keys（AI 与外部服务密钥隔离）
 - 内容：posts、comments、tags、post_tags、ratings（评分）、reports（举报）
 - 教务：courses、course_schedules、enrollments（可选自建或对接）
 - 地图：buildings、rooms、poi、routes（缓存与收藏）
 - IoT：devices、device_types、device_readings（time, value, meta）、alerts
 - AI：kb_documents、kb_chunks（向量）、conversations、messages、tool_invocations

 > 采用乐观锁/软删除；关键表加审计字段（created_by、updated_by、ip、ua）。

 ---

 ## 6. AI 助手方案设计
 - **检索增强**：
   - 知识来源：学校官网/学院通知 PDF/校规校纪、流程指引、食堂菜单/开放时间、常见问答；
   - 切分与嵌入：分块（段落/标题感知）、文本清洗、向量化；
   - 检索与重排：相似度召回 + 关键字 BM25 融合，rerank 优化 Top-k；
   - 引用与可解释性：所有回答附可点击引用卡片。
 - **工具调用（Tool/Function Calling）**：
   - 课表/教务、论坛、导航、IoT、天气、日程；
   - 权限与风控：按角色开放工具集，记录调用审计；
   - 失败重试与回退策略（离线/异常时的降级提示）。
 - **成本与质量**：
   - 意图分类：FAQ/闲聊不上模型或使用小模型；
   - 命中缓存（用户上下文 + 检索签名）；
   - 评测集：校园 100+ QA，离线评测与 A/B 模版对比。
 - **多提供方/离线策略**：
   - 优先本地 Ollama（Qwen/Llama），对长文案或高难问题再切云端；
   - 关键敏感字段脱敏或不出域。

 ---

 ## 7. 硬件接入设计
 ### 7.1 温湿度传感器（推荐必做）
 - **硬件**：ESP32 + DHT22；
 - **传输**：MQTT（QoS1），主题示例：`campus/iot/{deviceId}/sensor`；
 - **后端**：订阅 → 校验设备密钥 → 入库（PostgreSQL/TimescaleDB）→ 告警与看板；
 - **前端**：实时曲线（WebSocket 推送）+ 历史查询 + 阈值告警。

 ### 7.2 导航小车（可选高阶）
 - **栈**：ROS 2（Nav2）+ RPi4/Jetson + LiDAR/IMU/里程计；
 - **能力**：建图/定位/路径规划/避障；
 - **对接**：后端下发 POI 目标/路径，订阅 pose/状态，存历史轨迹；
 - **展示**：前端地图叠加小车位置/轨迹与任务状态。

 ---

 ## 8. 安全、合规与运维
 - **鉴权与权限**：JWT、RBAC、API 分级；敏感接口二次确认与审计日志。
 - **输入校验与风控**：统一参数校验、限流、IP/UA 风险规则；上传文件病毒/类型校验。
 - **数据安全**：密码哈希、敏感数据加密（如 API Key）、最小权限访问；
 - **CORS/CSRF/XSS/SQL 注入**：统一网关与安全中间件；
 - **日志与可观测性**：结构化日志、TraceID、指标监控、异常报警（Sentry）。

 ---

 ## 9. 开发与部署
 ### 9.1 环境要求
 - Node.js ≥ 18、pnpm/npm、Java 17、Docker Desktop

 ### 9.2 快速开始（建议）
 1. 克隆仓库并复制示例环境变量：`cp .env.example .env`（前后端分别维护）
 2. 启动基础设施（可选）：`docker compose up -d postgres redis minio emqx`
 3. 启动后端：`./gradlew bootRun` 或 IDE 运行
 4. 启动前端：`pnpm install && pnpm dev`
 5. 打开浏览器访问 Web，登录测试账户。

 ### 9.3 目录建议
 ```
 frontend/       # Vue 3 前端
 backend/        # Spring Boot 后端
 infrastructure/ # docker-compose、初始化脚本
 ai/             # 提示词、评测集、知识库导入工具
 hardware/       # 硬件固件、MQTT 主题约定、ROS 接口
 docs/           # 设计文档、API 规范、ER 图
 ```

 ---

 ## 10. 里程碑与工作量规划（建议 12~16 周）
 - 第 1-2 周：需求与范围冻结、数据/文档收集、地图与论坛原型
 - 第 3-4 周：后端骨架、用户/权限、帖子/评论、对象存储
 - 第 5-6 周：AI 基础（LangChain4j、pgvector、RAG 原型）、知识库导入
 - 第 7-8 周：工具调用打通（课表/导航/论坛检索）、引用与缓存
 - 第 9-10 周：IoT 温湿度链路（设备→MQTT→后端→前端看板）
 - 第 11-12 周：评测与优化、风控与审计、可观测性
 - 第 13-14 周：预答辩准备、性能与体验改进、文档完善
 - （可选并行）导航小车 POC：ROS 2 接口与地图展示

 > 工作量达标点：AI（RAG+工具调用+评测）、论坛完整 CRUD+搜索、导航与地图、IoT 全链路、权限与审计、容器化部署与监控。

 ---

 ## 11. 开题报告要点（可直接引用至开题）
 - **研究背景与意义**：校园信息分散、服务割裂；通用大模型缺乏校内知识与工具执行能力。
 - **研究内容**：前后端系统、AI 助手（RAG+工具）、论坛/导航/教务、IoT 接入、（可选）导航小车。
 - **技术路线**：Vue 3 + Spring Boot 3 + PostgreSQL/pgvector + Redis + MQTT +（可选）Ollama/云模型。
 - **创新点**：校内专属知识与引用、工具编排执行、离线优先/隐私、IoT 与 AI 的跨域融合。
 - **可行性**：现有开源与云服务支撑、按里程碑迭代可控、范围可裁剪。
 - **预期成果**：可运行的 Web/PWA、演示数据与仪表盘、完整技术文档与评测报告。

 ---

 ## 12. 许可证
 建议采用 MIT 或 Apache-2.0；如包含校内数据，需遵循学校信息安全规范与数据使用授权。

 ## 13. 参考资料
 - LangChain4j、pgvector、Ollama 文档
 - 高德/Mapbox 地图 SDK、Nav2（ROS 2）
 - MQTT（EMQX/Mosquitto）、TimescaleDB
 - Spring Security、JWT 最佳实践

 ---

 > 提示：本 README 为总体设计与执行蓝图，后续将在 `docs/` 内细化 API 规范、数据表设计、AI 评测流程与硬件接入手册。


