# 2026年3月〜4月 Tool Use（Skills）論文ニュースレター

## 全体トレンド

2026年2月のSkills研究は、AnthropicのSkill概念整理（SoK・サーベイ・SkillsBench）を契機に「Tool Useを包含する高次概念としてのSkill」が研究コミュニティの共通語彙として定着した段階だった。3〜4月になるとその上に**実装競争**が一気に乗り、論点は以下の3軸に明確に分化している。

第一に、**スキルの自動獲得・進化アーキテクチャの多様化**。AutoSkill・SkillX・SkillNet・EvoSkill・EvoAgent等が同時期に登場し、単一エージェントの蓄積から、検証器との共進化（CoEvoSkills）、ユーザ横断の集団進化（SkillClaw）、意思決定者とスキルバンクの二者共進化（COSPLAY）へと、**学習シグナルの分散化と非対称化**が進行している。

第二に、**スキルの「実体」をどう設計するか**という基礎研究の登場。SKILL.md形式の自然言語テキストから機械処理可能な構造化表現（SSL）への移行、推論時ロードからモデルパラメータへの内在化（SKILL0）、検索拡張パラダイムとしてのスキル取得（SRA）など、**スキルの永続化レイヤと取得経路**が一斉に問い直されている。さらに`Experience Compression Spectrum`がmemory・skills・rulesを「圧縮率の単一軸」上で統一し、コミュニティ間の分断を可視化した点も重要。

第三に、**スキルの実用性を疑う評価ベンチマークの乱立**。SkillCraft・SkillFlow・SkillLearnBench・"In the Wild"の4本が3〜4月に投入され、「与えられたスキルが使えるか」だけでなく、「経験から発見し失敗時に修復し一貫したライブラリを維持できるか」「現実的検索条件下でも有効か」を問う段階に進んだ。多くの論文が**「スキルの効果は理想化条件外では脆弱」「使用率の高さが必ずしも有用性に繋がらない」**と報告している。

加えて、ClawHub上のスキル30%超がsuspicious/maliciousと判定された`Red Skills or Blue Skills`は、スキルエコシステムの**サプライチェーンセキュリティ**を新たな課題として明示した。

共通する未解決課題は、(1) **スキルの粒度と抽象度**（細かすぎると冗長、粗すぎると汎化不能）、(2) **スキル使用の自己制御**（必要・不要を問わず同率でロードしてしまう傾向）、(3) **スキル共有の安全性**（悪意あるスキル混入・recursive drift）の3点に集約される。

---

## 1. スキルの自動獲得と進化アーキテクチャ

### 1.1 単一エージェントによるスキル蓄積・洗練

**🔍 このジャンルのトレンドと課題感**
スキルを「実行可能で再利用可能な能力単位」として明示的に管理し、エージェントが自ら発見・洗練・蓄積する設計が標準化しつつある。3〜4月の特徴は、単なるスキル蓄積を超えて**スキルライブラリ全体のオントロジー・接続性・評価基準まで含めて自動化する**方向への拡張。残課題は、転移可能な抽象度の確保と、スキル間の重複・矛盾の制御。

---

#### [Mar 2026] "AutoSkill: Experience-Driven Lifelong Learning via Skill Self-Evolution" [[paper](https://arxiv.org/abs/2603.01145)]
- **概要**: 対話・インタラクションのトレースからスキルを自動抽出・維持・再利用する経験駆動型生涯学習フレームワーク。基盤モデルを再学習せずに関連スキルを将来のリクエストへ動的注入し、モデル非依存のプラグイン層として既存LLMと互換。スキル表現を標準化してエージェント・ユーザ・タスク間で共有・転移可能。
- **差分・新規性**: ハルシネーション低減・組織固有のライティング規約・専門用語回避といった**反復するユーザ要件をスキルとして固着化する**点に焦点を当てた、生涯パーソナライゼーション志向の設計。

#### [Mar 2026] "EvoSkill: Automated Skill Discovery for Multi-Agent Systems" [[paper](https://arxiv.org/abs/2603.02766)]
- **概要**: 実行失敗の分析からスキルを自動発見・修正し、Pareto frontierで検証性能が向上したものだけを「スキルフォルダ」に保持する自己進化フレームワーク。OfficeQAで+7.3%、SealQAで+12.1%。
- **差分・新規性**: スキルレベル最適化が訓練タスクを超えて**ゼロショット転移**を生むことを実証した点。失敗駆動の選択淘汰でスキルライブラリを刈り込む設計が独自。

#### [Mar 2026] "SkillNet: Create, Evaluate, and Connect AI Skills" [[paper](https://arxiv.org/abs/2603.04448)]
- **概要**: AIスキルの「作成・評価・組織化」を可能にする統一インフラ。20万件超のスキルベース、対話的プラットフォーム、Pythonツールキットを提供し、Safety / Completeness / Executability / Maintainability / Cost-awareness の5次元評価オントロジーを採用。ALFWorld・WebShop・ScienceWorldで平均報酬+40%、実行ステップ-30%。
- **差分・新規性**: 個別エージェントのスキル進化に閉じず、**スキル相互の関係構築・横断評価まで含めた基盤インフラ**として位置づける点が他論文と異なる。スキル研究のエコシステム層を担う。

#### [Apr 2026] "SkillX: Automatically Constructing Skill Knowledge Bases for Agents" [[paper](https://arxiv.org/abs/2604.04804)]
- **概要**: 戦略プラン／機能スキル／原子スキルの3階層に蒸留したスキル知識ベースを完全自動構築。実行フィードバックによる反復洗練と、種データを超える新規スキルの能動生成・検証を組み合わせ、AppWorld・BFCL-v3・τ²-Benchで弱いベースエージェントに装着して一貫した改善を示す。
- **差分・新規性**: スキルを階層蒸留してプラグアンドプレイなライブラリ化する点が、AutoSkill（フラット）やSkillNet（オントロジー駆動）とは異なる構造的アプローチ。

#### [Apr 2026] "EvoAgent: An Evolvable Agent Framework with Skill Learning and Multi-Agent Delegation" [[paper](https://arxiv.org/abs/2604.20133)]
- **概要**: スキルを**トリガ機構と進化メタデータを備えたマルチファイル能力ユニット**としてモデル化し、ユーザフィードバック駆動の閉ループでスキルを継続生成・最適化。3段階スキルマッチングと3層メモリで動的タスク分解と長期能力蓄積を支える。GPT-5.2統合で平均約28%向上。
- **差分・新規性**: スキルに「いつ起動するか」のトリガを内在化させた設計。スキル選択／実行制御の側面まで進化対象に含めている。

---

### 1.2 共進化・集団進化によるスキル獲得

**🔍 このジャンルのトレンドと課題感**
スキルを単一エージェントが孤立して進化させるのではなく、**検証器・複数ユーザ・別エージェントとの共進化**で発見・洗練するアプローチが4月に集中。学習シグナルを生成器自身ではなく外部進化的アクターから取り出す方向で、コミュニティの問題意識が一致しつつある。

---

#### [Apr 2026] "CoEvoSkills: Self-Evolving Agent Skills via Co-Evolutionary Verification" [[paper](https://arxiv.org/abs/2604.01687)]
- **概要**: Skill Generatorがマルチファイル構造のSkillパッケージを反復洗練する一方、Surrogate Verifierが正解にアクセスせず情報的・実行可能なフィードバックを共進化させる。SkillsBenchでClaude Code・Codexの両方で5ベースラインに対し最高合格率。
- **差分・新規性**: 「生成器と検証器の共進化」というGAN的構造をSkill領域に持ち込んだ点。EvoSkillの失敗駆動選択淘汰よりも積極的に検証信号自体を進化させる。

#### [Apr 2026] "SkillClaw: Let Skills Evolve Collectively with Agentic Evolver" [[paper](https://arxiv.org/abs/2604.08377)]
- **概要**: マルチユーザーエージェント生態系での集団的スキル進化フレームワーク。クロスユーザー・時系列インタラクションを主シグナルとして、自律Evolverが繰り返し現れる行動パターンを抽出してスキルセットを更新し、共有リポジトリ経由で全ユーザーに同期する。WildClawBenchでQwen3-Maxを実世界エージェントシナリオで有意に改善。
- **差分・新規性**: 単一ユーザ・単一エージェント前提の他研究に対し、**ユーザ横断で改善が伝搬する社会的進化**を扱う数少ない研究。デプロイ後の継続的改善源を多数のユーザインタラクションに置く設計が独自。

#### [Apr 2026] "Co-Evolving LLM Decision and Skill Bank Agents for Long-Horizon Tasks" [[paper](https://arxiv.org/abs/2604.20987)]
- **概要**: 意思決定エージェントとスキルバンクエージェントを分離し共進化させるCOSPLAYフレームワーク。LLM意思決定エージェントは学習可能スキルバンクから検索して行動し、スキルパイプラインがラベルなしロールアウトから再利用可能スキルを発見・追加する。8Bベースで4つのフロンティアLLMに対し平均25.1%以上の報酬改善。
- **差分・新規性**: スキル発見と意思決定を**別々のエージェントとして共進化**させる二者構造。長期インタラクション環境（ゲーム）でラベルなしロールアウトのみで進化する点も特徴。

---

## 2. スキルの最適化と内在化

**🔍 このジャンルのトレンドと課題感**
スキルが「外付けライブラリとして用意するもの」から**「最適化対象あるいは重みに統合される対象」**へとパラダイムが拡張された。Bilevel MCTSが探索の理論的構造を、SKILL0が内在化のための学習カリキュラムを提示し、両者は補完的。

---

#### [Apr 2026] "Bilevel Optimization of Agent Skills via Monte Carlo Tree Search" [[paper](https://arxiv.org/abs/2604.15709)]
- **概要**: スキル最適化を「スキル構造」と「コンポーネント内容」の二層問題として定式化。外側ループでMCTSにより構造を探索し、内側ループでLLMが選ばれた構造内のコンポーネントを洗練する。Operations Research QAデータセットで性能改善。
- **差分・新規性**: スキル最適化に明示的な数理的定式化（bilevel optimization + MCTS）を導入した点。経験的洗練を主とする他のスキル系研究に対し、探索の理論的構造を与える。

#### [Apr 2026] "SKILL0: In-Context Agentic Reinforcement Learning for Skill Internalization" [[paper](https://arxiv.org/abs/2604.02268)]
- **概要**: スキルを推論時ロード（in-context）から**モデルパラメータに内在化**するフレームワーク。学習時に最初は完全なスキルコンテキストを与え、徐々に取り除くカリキュラムを採用。Dynamic Curriculumが各スキルの有用性を評価し、線形に減衰する予算内で現在のpolicyにとって有益なスキルだけを残す。ALFWorldで+9.7%、Search-QAで+6.6%。ステップ当たり0.5kトークン未満のコンテキストを維持。
- **差分・新規性**: 他のスキル研究がコンテキスト経由のスキル装着を前提とするのに対し、本論文は**「コンテキストから消す」方向に進化させる**逆向き設計。スキルを永続化レイヤとして重みに移すことで、推論コストとパフォーマンスを同時に改善する。

---

## 3. スキルの構造化と検索基盤

**🔍 このジャンルのトレンドと課題感**
SKILL.md形式の自然言語ドキュメントとして共有されてきたスキルが、**機械処理可能な構造化表現**と**RAG的な検索基盤**としての設計に再定義されつつある。スキル研究の「インフラ層」が固まり始めた段階。

---

#### [Apr 2026] "From Skill Text to Skill Structure: The Scheduling-Structural-Logical Representation for Agent Skills (SSL)" [[paper](https://arxiv.org/abs/2604.24026)]
- **概要**: text-heavyなSKILL.md形式に対し、**Schank & Abelsonの認知科学理論**（Memory Organization Packets, Script Theory, Conceptual Dependency）に基づき、スキルレベルのscheduling信号、sceneレベルの実行構造、logicレベルのaction/resource使用エビデンスを分離した構造化表現。Skill Discoveryで MRR 0.573→0.707、Risk Assessmentで macro F1 0.744→0.787。
- **差分・新規性**: スキルの**メタ構造を認知科学から借用して設計**する点が他のスキル研究と異なる。検索・レビューを容易にする出典付き構造を提供し、エコシステムの整備に資する。

#### [Apr 2026] "Skill Retrieval Augmentation for Agentic AI (SRA)" [[paper](https://arxiv.org/abs/2604.24594)]
- **概要**: LLMエージェントが大規模外部コーパスから関連スキルを動的に検索するパラダイムをSRAとして定式化。5,400テストインスタンス・636件の手作業構築gold skillsを26,262件と混ぜたSRA-Benchを提示。検索ベースのskill augmentationはエージェント性能を改善するが、**現状のLLMはgold skillが検索されているか・必要かに関係なく同程度の率でスキルをロードしてしまう**傾向を発見。
- **差分・新規性**: スキル研究をRAGの枠組みに正面から接続した点。「スキルが検索されたから使う」という無批判な使用傾向を指摘し、選択的取得への課題を明示。

---

## 4. 理論的統合：メモリ・スキル・ルールの統一視点

**🔍 このジャンルのトレンドと課題感**
4月の最も重要な理論的貢献として、メモリ・スキル・ルールという**従来別個に扱われてきた概念を「圧縮率」で統一**する視点が登場。Skills研究とMemory研究の相互引用率が1%未満という実証データに基づき、コミュニティ統合の必要性が提示された。

---

#### [Apr 2026] "Experience Compression Spectrum: Unifying Memory, Skills, and Rules in LLM Agents" [[paper](https://arxiv.org/abs/2604.15877)]
- **概要**: agent memoryシステムとagent skill discoveryが同じ課題（インタラクション軌跡から再利用可能な知識を抽出）に取り組むにもかかわらず、22本の主要論文・1,136件の引用分析の結果、両コミュニティ間の相互引用率は1%未満であることを実証。**Experience Compression Spectrum**を提案し、memory（5–20×圧縮）・procedural skills（50–500×）・declarative rules（1,000×+）を圧縮率の単一軸上の点として統一。20以上のシステムをマップした結果、各システムは固定圧縮レベルで動作しており、適応的なcross-level圧縮（"missing diagonal"）が欠落していることを指摘。
- **差分・新規性**: 個別の手法提案ではなく、**コミュニティ全体の構造的分断を計量的に可視化し統一フレームワークで再編する**メタ的貢献。「両コミュニティが独立に同じ部分問題を解いている」という指摘は今後の研究の前提を変える可能性が高い。

---

## 5. スキル評価ベンチマーク

**🔍 このジャンルのトレンドと課題感**
2月のSkillsBench以降、評価ベンチマークが急増。3〜4月には4本が立て続けに登場し、評価の問いが「与えられたスキルを使えるか」から**「発見・修復・維持できるか」「現実的設定で有効か」**へ進化。共通する報告は「理想化条件外ではスキルの効果が脆弱」「スキル使用率と有用性は乖離する」というもの。残課題は、ベンチマーク間の指標統一とリアリティの担保。

---

#### [Mar 2026] "SkillCraft: Can LLM Agents Learn to Use Tools Skillfully?" [[paper](https://arxiv.org/abs/2603.00718)]
- **概要**: エージェントが原子的ツール呼出から高次のツール合成（Skills）をどれだけ形成・再利用できるかを評価するベンチマーク。スキル合成・キャッシュ・再利用プロトコルを提案し、トークン使用量を最大80%削減。テスト時のツール合成能力と成功率に強い相関を観察。
- **差分・新規性**: 「Tool UseがSkillに進化する」という命題自体を**実証的に評価する**初期ベンチマーク。Skillsという概念の意義を経験的に支える基礎研究。

#### [Apr 2026] "How Well Do Agentic Skills Work in the Wild" [[paper](https://arxiv.org/abs/2604.04323)]
- **概要**: 既存skillベンチマークの「タスクごとに専用スキルが直接与えられる」理想化条件を批判し、エージェントが34k件の実世界スキルから自分で検索・選択する現実的設定下で初の包括的研究を実施。スキルの効果は脆弱で、設定が現実的になるほど改善幅は減衰し、最も困難な条件ではno-skillベースラインに近づく。query-specific refinementで Terminal-Bench 2.0 でClaude Opus 4.6を57.7%→65.5%に回復。
- **差分・新規性**: 「実世界では既存ベンチほど効かない」という冷静な実証結果。スキル研究の楽観的主張に対する重要な歯止め。

#### [Apr 2026] "SKILLFLOW: Benchmarking Lifelong Skill Discovery and Evolution for Autonomous Agents" [[paper](https://arxiv.org/abs/2604.17308)]
- **概要**: 20ファミリ・166タスクから成り、各ファミリ内のタスクが`Domain-Agnostic Execution Flow (DAEF)`に従う。エージェントはスキル無しで開始しタスクを順に解き、trajectory・rubricベースのskill patchesを生成して次タスクに引き継ぐ。Claude Opus 4.6でlifelong skill evolutionにより62.65%→71.08%（+8.43pt）。Kimi K2.5は66.87%のスキル使用率にも関わらず+0.60ptのみ、Qwen-Coder-Nextは退化。
- **差分・新規性**: スキル使用率と有用性が乖離するという**負のシグナルの実証**。スキル研究にとって「使えるスキル」と「使われるスキル」の差を数値化した重要な貢献。

#### [Apr 2026] "SkillLearnBench: Benchmarking Continual Learning Methods for Agent Skill Generation on Real-World Tasks" [[paper](https://arxiv.org/abs/2604.20087)]
- **概要**: 実世界skill taxonomyに基づく15サブドメイン・20タスクで、skill quality / execution trajectory / task outcomeの3レベルで評価。one-shot、self/teacher feedback、skill creator等の継続学習手法を評価し、全手法がno-skillを上回るが、**全タスク・全LLMで勝つ手法はなく、より強いLLMへのスケーリングも信頼できる助けにならない**ことを報告。複数イテレーションは外部feedbackと組合せれば真の改善をもたらすが、**self-feedback単独はrecursive driftを誘発**。
- **差分・新規性**: 「self-feedback単独で改善する」という近年の自己進化系研究の前提に対し、**外部フィードバックとの組合せが必須**という強い反証を提示。SKILLFLOWが評価設計、SkillLearnBenchが手法比較に焦点を置く相補関係。

---

## 6. スキルエコシステムとセキュリティ

**🔍 このジャンルのトレンドと課題感**
スキルがプラットフォームを介して共有される時代に入り、**サプライチェーンセキュリティ**が研究対象として登場。2月のSoK論文で予告されていた懸念が、実データに基づく定量分析として現実化した。

---

#### [Apr 2026] "Red Skills or Blue Skills? A Dive Into Skills Published on ClawHub" [[paper](https://arxiv.org/abs/2604.13064)]
- **概要**: ClawHubから収集した26,502件のスキルを分析。英語スキルはAPI・自動化・メモリ等インフラ寄り、中国語スキルはメディア生成・SNS・金融等application-orientedで、文化圏ごとのスキル分布の偏りを示す。**収集スキルの30%超がプラットフォームのシグナルによりsuspicious / maliciousと判定**。投稿時情報のみを用いた最良分類器でaccuracy 72.62%・AUROC 78.95%を達成し、**ドキュメント品質がリスクの最も予測力ある指標**であることを示した。
- **差分・新規性**: スキル研究において**実データに基づく初の脅威分析**。SkillClaw・SkillNet・SkillXがスキル共有エコシステムを前提としていることを踏まえると、本論文の問題提起はインフラ設計の制約条件として重要。

---
