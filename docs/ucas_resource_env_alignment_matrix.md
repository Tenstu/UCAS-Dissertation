# UCAS 资环规范对照矩阵（Word 基线）

本文用于固定 `UCAS-Dissertation` 的资环一致性验收口径，避免后续口径漂移。

说明：

- “具体要”原文件是 `.doc`，当前仓库环境无法直接完整结构化解析；本矩阵采用“双证据”固化基线：  
  1) `docs/official/中国科学院大学资源与环境学位评定分委员会研究生学位论文撰写具体要.doc` 可提取条款关键词；  
  2) `docs/official/中国科学院大学资环学科群研究生学位论文word模板.docx` 的实际结构与样式结果（作为实现基线）。
- 验收优先看“结构与版式规则是否满足”；内容型规则（如摘要字数）单独按正文最终稿核查。
- 当前 DOCX 导出能力仅作为本仓库项目级对齐参考，不构成全自动终稿链路；图表/算法/代码环境/交叉引用等复杂对象仍需人工复核，后续持续优化。

## 对照矩阵

| 条款主题 | Word 模板基线（证据） | UCAS LaTeX 落点 | 检查方式 |
|---|---|---|---|
| 纸张 A4（210x297mm） | 质量检查 `page=11906x16838 twip` | `styles/ucasDissertation.cls` 的 `geometry a4paper` | `scripts/export_docx.py` 质量报告 |
| 页边距与页眉页脚 | 质量检查 `top/bottom=1440`, `left/right=1797`, `header/footer=851` | `styles/ucasDissertation.cls` 中 `top/bottom=2.54cm`, `left/right=3.17cm`, `headheight/headsep/footskip` | `scripts/export_docx.py` 质量报告 |
| 正文字体（宋体 + Times New Roman） | 质量检查 Normal 样式字体通过 | `ctexbook + fontspec + \\setmainfont{Times New Roman}` | `scripts/export_docx.py` 质量报告 |
| 正文行距/首行缩进（1.25 倍/两字符） | 质量检查 `line=300, firstLine=200` | `linespread=1.25` + `autoindent=true` | `scripts/export_docx.py` 质量报告 |
| 标题不超过三级（0083） | Word 模板 `Heading4=0` | `secnumdepth=subsection` + 对 `\\subsubsection` 及更深层级告警并降级为不入目录/不编号 | LaTeX 编译日志 + 质量报告 |
| 目录不超过三级（0036） | Word 模板目录域层级不超三级 | `\\setcounter{tocdepth}{2}` | `scripts/export_docx.py` 质量报告（目录域未更新可提示） |
| 结构必备：摘要/Abstract | Word 模板存在 | `abstract.tex` + `\\makeAbstract` | `scripts/export_docx.py` 质量报告 |
| 结构必备：目录/图表目录 | Word 模板存在目录、图目录、表目录 | `Thesis.tex` 的 `\\tableofcontents` + `\\listofmaterials`；DOCX 导出补齐结构标题 | `scripts/export_docx.py` 质量报告 |
| 结构必备：参考文献 | Word 模板存在 | `Thesis.tex` 的 `\\printbibliography`；DOCX 导出补齐“参考文献”标题 | `scripts/export_docx.py` 质量报告 |
| 结构必备：致谢 | Word 模板存在 | `acknowledgements.tex` | `scripts/export_docx.py` 质量报告 |
| 书脊页 | Word 模板提供“书脊”页 | `styles/ucasDissertation.cls` 的 `\\makeSpine` + `spine.tex` 独立构建入口 | `xelatex spine.tex` |
| 参考文献规范（GB/T 7714-2015） | `.doc` 可提取到 `GB/T 7714 2015` | `biblatex` 使用 `gb7714-2015` | 构建日志 + PDF 复核 |

## 验收命令（项目级）

```bash
xelatex -interaction=nonstopmode Thesis.tex
biber Thesis
xelatex -interaction=nonstopmode Thesis.tex
xelatex -interaction=nonstopmode Thesis.tex
xelatex -interaction=nonstopmode spine.tex
python3 scripts/export_docx.py --project-dir . --tex-file Thesis.tex
```

通过标准（本轮）：

- 质量报告中以下项应为 `PASS`：纸张、页边距、字体行距、正文不超三级、章节存在（摘要/Abstract/目录/图表目录/参考文献/致谢）。
- “目录域未更新”允许保留提示，不作为失败项。
