---
title: "Agent Skill作成時の雑なTips 3選"
emoji: "💻"
type: "tech"
topics: [ClaudeCode, AI, AgentSkill, 生成AI]
published: false
---
# 記事の概要
Skillを書いていて困るのは、**修正するたびにSkillそのものの品質がどうなっているのかが分からない** ことである。

- Skillは自然言語で書かれているため、コードのようにテストで評価しにくい
- 公式の skill-creator の [eval](https://github.com/anthropics/skills/blob/main/skills/skill-creator/SKILL.md#running-and-evaluating-test-cases) で評価はできるが、毎回回すのも重い

そこで、**明らかな品質劣化を防ぐ** ための、実用的で雑なTipsを3つ紹介する。

# Tips 1: 公式ベストプラクティスをAIにレビューさせる
書き上げたSkillを、別のClaudeセッションに渡して以下を指示する。

> [公式のSkill authoring best practices](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices) を参照して、このSkillをレビューして。

すると公式の観点に沿った指摘がいくつも返ってくる。

ここで全部適用する必要はないため、SKillの用途や規模に合わせて、**返ってきた指摘の中から、自分が必要だと思うものだけを選び**、AIに適用を指示する。

# Tips 2: Progressive disclosure と フィードバックループだけは採用しておく
Tips 1のレビューをやると、ほぼ毎回出てくる指摘が2つある。

- **Progressive disclosure**: `SKILL.md` を目次にして、詳細を `references/` に分割する
- **フィードバックループ**: 手順に「検証 → 失敗なら修正 → 再実行」のループを組み込む

この2つは効果が大きく、ほとんどのSkillに当てはまる。Tips 1で他の指摘を捨てても、この2つは原則として採用するつもりでいると品質が安定する。

> Progressive disclosureとフィードバックループを適用して。

これだけで、`SKILL.md` の分割と検証ループの差し込みを一気にやってくれる。

# Tips 3: Tips 1, 2 を「書き換え後の軽量検証フェーズ」に組み込む
Skill運用で一番しんどいのは、修正後に品質が劣化していないかを確認するフェーズである。evalを毎回回すのは重いし、目視レビューでデグレを見つけ切るのも難しい。

ここで Tips 1 と Tips 2 を **書き換え後の軽量な検証** として習慣化すると話が変わる。

Skillを書き換えたら毎回、

1. Tips 1: AIに公式ベストプラクティスでレビューさせる
2. Tips 2: PDとフィードバックループが崩れていないか確認させる

を回す。これだけで「明らかな品質劣化」はかなり拾える。本格的なevalを回す前のフィルタとして機能するので、軽い修正のたびに重い検証を回さずに済む。

# まとめ
1. 公式ベストプラクティスを参照してAIにレビューさせる(取捨選択は自分)
2. Progressive disclosureとフィードバックループだけは原則採用する
3. 書き換えのたびにTips 1, 2を検証フェーズに組み込む

# 参考
- [Skill authoring best practices(公式)](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices)
- [skill-creator eval(公式GitHub)](https://github.com/anthropics/skills/blob/main/skills/skill-creator/SKILL.md#running-and-evaluating-test-cases)
