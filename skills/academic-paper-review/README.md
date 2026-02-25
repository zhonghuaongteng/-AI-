# Academic Paper Review Skill

[![Version](https://img.shields.io/badge/version-2.0.0-blue.svg)](https://github.com/zhonghuaongteng/-AI-.git)
[![Claude Code](https://img.shields.io/badge/Claude_Code-Skill-green.svg)](https://claude.ai/code)

A comprehensive academic paper review system for Claude Code, featuring multiple review modes, AI writing detection, and professional academic standards.

## Features

### Three Review Modes

| Mode | Description | Output |
|------|-------------|--------|
| **Standard** | Four-round comprehensive review with detailed feedback | Issue report HTML |
| **Agent Team** | Three-role parallel review (Editor + Reviewer + Author) | Separate role reports + final report |
| **Auto-Modify** | Review + automatic document modification with diff comparison | Modified document + comparison report |

### Four-Round Review System

1. **Basic Language & Format Check** - Typos, grammar, punctuation, format compliance
2. **Academic Expression Review** - Tone, flow, precision, structure
3. **AI Writing Detection** - Content patterns, language patterns, style analysis
4. **Professional Harsh Review** - Research contribution, methodology rigor, innovation

## Installation

1. Clone this repository or copy the `academic-paper-review` folder to your Claude Code skills directory:

```bash
# Default skills location
~/.claude/skills/academic-paper-review/
```

2. The skill will be automatically available in Claude Code.

## Usage

Activate the skill by submitting an academic paper for review:

```
Please review my academic paper: [file path]
```

You will be prompted to select a review mode:

```
请选择审查模式：

1. 标准审查 - 四轮审查，输出问题清单和建议
2. 团队审查 - 编辑+审稿人+作者三角色协作审查
3. 自动修改 - 审查后自动修改论文并生成对比报告

请输入模式编号 (1/2/3)：
```

## Output Structure

After review, all outputs are organized in the `审阅报告/` folder:

```
审阅报告/
├── {论文名}_{timestamp}_问题检测清单.html    # Standard/Team mode
├── {论文名}_{timestamp}_全文对照修改.html    # Standard/Team mode
├── {论文名}_{timestamp}_编辑报告.json        # Team mode
├── {论文名}_{timestamp}_审稿人报告.json      # Team mode
├── {论文名}_{timestamp}_最终报告.html        # Team mode
├── {论文名}_修改版.md                        # Auto-Modify mode
├── 修改对比报告.html                          # Auto-Modify mode
├── changes.diff                              # Auto-Modify mode
├── author_result.json                        # Auto-Modify mode
└── review_history.json                      # All modes
```

## Project Structure

```
academic-paper-review/
├── SKILL.md                    # Main skill configuration and workflow
├── README.md                   # This file
├── assets/
│   ├── review_template.html           # Default HTML review template
│   ├── review_template_cyber.html     # Cyber-styled HTML template
│   └── review_template_demo.html      # Demo HTML template
└── references/
    ├── review_standards.md            # Academic writing standards
    └── ai_detection_patterns.md       # AI writing detection patterns
```

## Reference Materials

### `references/review_standards.md`
Comprehensive evaluation criteria including:
- Common rejection reasons
- Academic writing guidelines
- Format requirements
- Research contribution evaluation
- Paper structure standards

### `references/ai_detection_patterns.md`
AI writing detection patterns covering:
- Content patterns (significance inflation, promotional language)
- Language patterns (AI vocabulary, copula avoidance)
- Style patterns (em dash overuse, formatting issues)
- Academic-specific AI patterns

## Language Support

- Chinese papers (中文论文)
- English papers (英文论文)

The skill automatically detects the paper language and provides feedback in the same language.

## Supported Document Types

- Theses (学位论文)
- Journal articles (期刊文章)
- Conference papers (会议论文)
- Research reports (研究报告)

## Version History

- **v2.0.0** - Three-mode review system with agent team collaboration
- **v1.0.0** - Initial release with standard four-round review

## Contributing

Contributions are welcome! Please feel free to submit issues or pull requests.

## License

This project is part of the [AI Repository](https://github.com/zhonghuaongteng/-AI-.git).

## Acknowledgments

Developed as a Claude Code Skill for academic paper review and quality improvement.
