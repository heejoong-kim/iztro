# Ziwei Formatter Policy for Arahan MVP

## Purpose

This document records the formatter policy for using `iztro` in the Arahan MVP.

- `iztro` is used only as a Ziwei calculation engine.
- Arahan must not expose raw `iztro` output directly to end users.
- Arahan service code must use a wrapper/formatter layer to convert raw terms into consumer-friendly Korean.
- This document exists to prevent the formatter requirement from being forgotten during Arahan service implementation.

## Core Principles

- Do not modify `iztro` source for Korean product wording.
- Keep `iztro` as a calculation engine.
- Apply Arahan-specific display formatting outside `iztro`.
- Do not prominently expose 연간지, 년주, or “00년 출생” on the user-facing Ziwei result screen.
- Use 명궁, 신궁, 재백궁, 관록궁, 부부궁, 대표 주성, and 사화 요약 as the main display focus.
- Keep raw fields for storage, debugging, and AI prompt context only.

## Required Display Term Mapping

| iztro raw output | Arahan display term | Note |
|---|---|---|
| 부처 | 부부궁 | Avoid confusion with Buddhist meaning |
| 질액 | 건강궁 | Consumer-friendly wording |
| 노복 | 교우궁 | More modern wording |
| 재백 | 재백궁 | Palace suffix added |
| 관록 | 관록궁 | Palace suffix added |
| 전택 | 전택궁 | Palace suffix added |
| 복덕 | 복덕궁 | Palace suffix added |
| 부모 | 부모궁 | Palace suffix added |
| 형제 | 형제궁 | Palace suffix added |
| 자녀 | 자녀궁 | Palace suffix added |
| 록 | 화록 | Four transformation display |
| 권 | 화권 | Four transformation display |
| 과 | 화과 | Four transformation display |
| 기 | 화기 | Four transformation display |
| 대한 | 대운 | Consumer-friendly fortune term |
| 유년 | 세운 또는 올해 운 | Consumer-friendly fortune term |
| 유월 | 월운 | Consumer-friendly fortune term |
| 유일 | 일운 | Consumer-friendly fortune term |
| 유시 | 시운 | Consumer-friendly fortune term |
| 소한 | 소운 또는 숨김 | Hide in MVP unless needed |
| 어린 | 유년기 또는 숨김 | Hide in MVP unless needed |
| 아침 자시 | 조자시 | More natural Korean term |
| 밤에 자시 | 야자시 | More natural Korean term |
| 여자 | 여성 | UI label |
| 남자 | 남성 | UI label |
| 수이국 | 수2국 또는 수이국(水二局) | More readable |
| 목삼국 | 목3국 또는 목삼국(木三局) | More readable |
| 금사국 | 금4국 또는 금사국(金四局) | More readable |
| 토오국 | 토5국 또는 토오국(土五局) | More readable |
| 화육국 | 화6국 또는 화육국(火六局) | More readable |

## Fields Not to Expose Prominently

- `chineseDate`
- `rawDates`
- `rawDates.chineseDate`
- `rawDates.lunarDate`
- `heavenlyStem`
- `earthlyBranch`
- `earthlyBranchOfSoulPalace`
- `earthlyBranchOfBodyPalace`
- `isOriginalPalace`
- `originalPalace`
- `라인`
- `copyright`
- 한자식 `lunarDate`
- 연간지/년주/“00년 출생” 강조 문구

## Recommended Formatter Functions

- `formatZiweiForDisplay(rawAstrolabe)`
- `formatZiweiForPrompt(rawAstrolabe)`
- `formatZiweiMetadata(input, rawAstrolabe, config)`

## MVP Calculation Policy Memo

```json
{
  "yearDivide": "normal",
  "horoscopeDivide": "normal",
  "dayDivide": "forward",
  "algorithm": "default",
  "fixLeap": true,
  "language": "ko-KR"
}
```

## Completion Criteria

- User-facing display JSON must not contain awkward raw terms such as 부처, 질액, 노복, 대한, 록, 권, 과, 기, 밤에 자시, 여자.
- User-facing display JSON should use 부부궁, 건강궁, 교우궁, 대운, 화록, 화권, 화과, 화기, 야자시, 여성.
- 연간지, 년주, and “00년 출생” must not be emphasized in the top summary or main cards.
- Raw fields may be kept for storage, debugging, and AI prompt context.
- Final implementation should be done in the Arahan service wrapper/formatter layer, not by changing `iztro` calculation logic.
