# Arahan Ziwei Verification Summary

## Purpose

This document summarizes the verification work for using the `iztro` Ziwei library in the Arahan MVP.

- `iztro` is not an Arahan-owned library. It is an external open-source Ziwei calculation engine under the MIT license.
- Arahan should not connect `iztro` raw output directly to the user-facing UI.
- Arahan must use a wrapper/formatter layer.
- The final implementation location is the Arahan service repository, not this `iztro` fork.

## Current Decision Summary

- `iztro` is usable as a candidate engine for the Arahan MVP Ziwei menu.
- Do not modify `iztro` calculation source.
- Arahan service wrapper should normalize input and call `iztro`.
- Arahan formatter should convert `iztro` raw output into consumer-friendly Korean.
- The Ziwei menu should start as a supporting/upsell product, not the primary MVP product.
- Do not prominently expose 연간지, 년주, or “00년 출생” in the user-facing screen.

## Verification Results

| Area | Result | Decision |
|---|---|---|
| Existing tests | Existing tests were checked and passed in prior verification | Library baseline looks acceptable |
| Sample astrolabe output | Core fields could be output for 5 sample cases | Continue MVP review |
| timeIndex mapping | `HH:mm` must be converted to `timeIndex` 0-12 | Handle in wrapper |
| dayDivide | Default is `forward` | Treat 23:00-23:59 as 야자시 |
| yearDivide | Default is `normal` | Explicitly use `normal` in MVP initially |
| horoscopeDivide | Default is `normal` | Explicitly use `normal` in MVP initially |
| bySolar vs byLunar | 5 general-date cases matched | Solar/lunar input can be supported |
| leap month | `isLeapMonth` and `fixLeap` can affect results | Do not ask leap month on default MVP screen |
| ko-KR locale | Some Korean terms are awkward | Formatter is required |
| UI policy | Year-pillar emphasis may confuse users | Hide it from main UI |

## Default Calculation Config

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

- MVP starts by explicitly using the `normal/normal` configuration compatible with `iztro` defaults.
- If `yearDivide` or `horoscopeDivide` changes to `exact`, results around 입춘 can change, including 오행국, 사화, and 대운 방향.
- Switching to `exact` should be decided later after comparing with external Ziwei sites.

## Input UX Policy

- Default birthdate input should be solar calendar.
- Users choose only solar or lunar calendar.
- Do not ask leap-month status on the default input screen.
- Most users may not know whether they were born in a leap month, so asking can create drop-off.
- Lunar input is treated as a regular lunar month by default.
- Leap-month handling is managed as an advanced option, internal policy, or support response.
- Birth time is collected as `HH:mm`, then converted to `timeIndex` 0-12 before calling `iztro`.

## Wrapper Responsibilities

- [ ] Convert `HH:mm` to `timeIndex` 0-12.
- [ ] Treat 00:00-00:59 as `timeIndex` 0, 조자시.
- [ ] Treat 23:00-23:59 as `timeIndex` 12, 야자시.
- [ ] Use `bySolar()` for solar input.
- [ ] Use `byLunar()` for lunar input.
- [ ] Treat lunar input as `isLeapMonth=false` by default.
- [ ] Explicitly use `fixLeap=true`.
- [ ] Explicitly set `yearDivide`, `horoscopeDivide`, `dayDivide`, and `algorithm`.
- [ ] Never expose `iztro` raw output directly to user-facing UI.
- [ ] Use `formatZiweiForDisplay()` to create display JSON.
- [ ] Use `formatZiweiForPrompt()` to create AI prompt JSON.
- [ ] Use `formatZiweiMetadata()` to create storage/reproducibility metadata.

## Formatter Must Convert

| iztro raw | Arahan display |
|---|---|
| 부처 | 부부궁 |
| 질액 | 건강궁 |
| 노복 | 교우궁 |
| 재백 | 재백궁 |
| 관록 | 관록궁 |
| 록 | 화록 |
| 권 | 화권 |
| 과 | 화과 |
| 기 | 화기 |
| 대한 | 대운 |
| 유년 | 세운 또는 올해 운 |
| 유월 | 월운 |
| 유일 | 일운 |
| 유시 | 시운 |
| 아침 자시 | 조자시 |
| 밤에 자시 | 야자시 |
| 여자 | 여성 |
| 남자 | 남성 |
| 수이국 | 수2국 또는 수이국(水二局) |
| 목삼국 | 목3국 또는 목삼국(木三局) |
| 금사국 | 금4국 또는 금사국(金四局) |
| 토오국 | 토5국 또는 토오국(土五局) |
| 화육국 | 화6국 또는 화육국(火六局) |

## Fields Not to Emphasize in UI

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
- 한자식 `lunarDate`
- 연간지
- 년주
- “00년 출생” 강조 문구

## Recommended MVP Display Sections

- Top summary
- 명궁 card
- 신궁 card
- 재백궁 card
- 관록궁 card
- 부부궁 card
- 대표 주성 summary
- 사화 summary
- 대운 summary
- 12궁 상세 collapsed section

## Required Stored Metadata

- `calendarType`
- `solarDate`
- `lunarYear`
- `lunarMonth`
- `lunarDay`
- `isLeapMonth`
- `fixLeap`
- `timeIndex`
- `gender`
- `iztroVersion`
- `yearDivide`
- `horoscopeDivide`
- `dayDivide`
- `algorithm`
- `generatedAt`

## Existing Policy Documents

Also refer to:

- `docs/ziwei-formatter-policy.md`
- `docs/ziwei-integration-checklist.md`

## Final Implementation Reminder

- This document is not for modifying `iztro` source.
- This `iztro` fork is used for verification and policy records.
- Actual wrapper/formatter implementation must happen in the Arahan service repository.
- Refer to this document together with the two policy documents above.
- Do not connect `iztro` raw output directly to UI.
