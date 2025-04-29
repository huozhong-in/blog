# 本次发布的模型包括: 
> MoE 模型： Qwen3-235B-A22B (MoE, 总大小235B, 激活参数22B, 上下文128K) Qwen3-30B-A3B (MoE, 总大小30B, 激活参数3B, 上下文128K) 

> 非 MoE 模型： Qwen3-32B Qwen3-14B Qwen3-8B Qwen3-4B Qwen3-1.7B Qwen3-0.6B 

新版本的 Qwen3 特性如下: 
- 混合思维模式, 搭载了 thinking 开关, 可以直接手动控制要不要开启 thinking 
- 多语言支持, 支持 119 种语言和方言 
- Agent 能力提升, 提升了编码和 Agent 方面的表现，并加强了 MCP 的支持

另外, 打开就能直接体验的 [http://chat.qwen.ai](https://t.co/tkl4mjioQW) 的 Qwen3模型也上线了啦!
技术报告地址: [http://qwenlm.github.io/blog/qwen3/](http://qwenlm.github.io/blog/qwen3/) 
模型地址: [https://modelscope.cn/collections/Qwen3-9743180bdc6b48](https://modelscope.cn/collections/Qwen3-9743180bdc6b48)
Github Repo: [http://github.com/QwenLM/Qwen3](http://github.com/QwenLM/Qwen3)

![[Pasted image 20250429074935.png]]

# 咱们来看看它的能力
## Qwen3-30B模型全面超越DeepSeek-V3
![[Pasted image 20250429075155.png]]
我们能看到它跟Google开源的Gemma3-27B-IT和DeepSeek-V3的对比，一台4090显卡就能跑4bit量化模型，又解锁很多场景了。
## 可以跟Google闭源模型Gemini2.5-Pro掰掰手腕
![[Pasted image 20250429075549.png]]
阿里这活儿干得漂亮！就是在上下文长度方面，Gemini动辄一两兆，Qwen3是128K，当然这跟用来跑它的显卡显存有直接关系，Google的自研TPU强到爆炸，显存也是自家的，性价比高。
## Qwen3的意义在哪儿？我的看法
我是开源技术派，也非常在意数据隐私保护。
- Qwen2.5-32B一直是我本地任务的主力，因为它不论生成代码的能力、使用工具的能力、对中文的支持，综合比较下来是最好的！是本地智能体的智力中枢！
- 经过DeepSeek蒸馏的R1-qwen-70B是能在本地电脑跑起来的最好的推理模型。
- Qwen系列的小尺寸模型被开源社区广泛用在语音生成类任务中，对世界的贡献不为外行所知。
- Qwen2.5-vl-72B在视觉任务的能力让人惊讶，不比Google Gemma3-72B差。
- Qwen2.5-code-32B用来驱动依赖编程能力的AI Agent框架效果不错。
- Qwen-audio支撑的语音生成和语音识别被国内很多项目拿来做虚拟人。

**Qwen3系列开源模型是开源基座模型的一次跃升！其超强的综合能力广泛支持下游任务，是“单点能力突破”型DeepSeek暂时无法比的，这点要客观看待。**
# 开源生态迅速跟进

开源生态到底有多大活力，你看看qwen3放出不到十二小时，各种评测和优化就来了。
## gguf格式
gguf转换，让llama.cpp能跑，也就是ollama和lm-studio能跑了
## mlx社区
mlx-community是Apple在自家芯片上为AI加速的底层库，它也跟进很快。跑在M2 Ultra上28token每秒的生成速度超过人的阅读速度：
> @awnihannun Qwen3 235B MoE (22B active) runs so fast on an M2 Ultra with mlx-lm. 
> - 4-bit model uses ~132GB - Generated 580 tokens at ~28 toks/sec

## KTransformers
这个当初能让单4090跑DS的框架，这是熬夜跟进啊
![[CleanShot 2025-04-29 at 08.06.33@2x.png]]
详见: https://github.com/kvcache-ai/ktransformers/blob/main/doc/en/AMX.md

# 使用Qwen3的“最佳实践”
最后，有人 @ivanfioravanti 已经放出了Qwen3最佳实践：
 
> 为了达到最佳性能，我们建议采用以下设置： 
> 采样参数：对于思考模式 (enable_thinking=True)，请使用Temperature=0.6、TopP=0.95、TopK=20 和 MinP=0。请勿使用贪婪解码，因为它会导致性能下降和无休止的重复。 
> 
> 对于非思考模式（enable_thinking=False），建议使用Temperature=0.7、TopP=0.8、TopK=20、MinP=0。 对于支持的框架，您可以将 presence_penalty 参数调整为 0 到 2 之间，以减少无限重复。但是，使用较高的值偶尔可能会导致语言混合，并略微降低模型性能。 
> 
> 足够的输出长度：我们建议大多数查询的输出长度为 32,768 个词元。对于高度复杂的问题（例如数学和编程竞赛中遇到的问题），我们建议将最大输出长度设置为 38,912 个词元。这为模型提供了足够的空间来生成详细而全面的响应，从而提升其整体性能。 
> 
> 标准化输出格式：我们建议在基准测试时使用提示来标准化模型输出。
> 
> 数学问题：包括“请逐步推理，并将您的最终答案放在提示中的 \boxed {}."内。 
> 
> 多项选择题：在提示中添加以下 JSON 结构以标准化响应：“请在答案字段中仅使用选择字母显示您的选择，例如“答案”：“C”。 
> 
> 历史中不包含思考内容：在多轮对话中，历史模型输出应仅包含最终输出部分，无需包含思考内容。Jinja2 提供的聊天模板已实现这一点。然而，对于不直接使用 Jinja2 聊天模板的框架，开发者需要确保遵循最佳实践。