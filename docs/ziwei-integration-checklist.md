# Ziwei Integration Checklist for Arahan MVP

## Purpose

This document is the integration checklist for using the `iztro` Ziwei calculation engine in the Arahan MVP.

- `iztro` is used only as a calculation engine.
- Arahan service code must handle input conversion, output formatting, and stored metadata through wrapper/formatter layers.
- This document exists to prevent implementation omissions later.

## Input UX Policy

- Default birthdate input should be solar calendar.
- Users should choose only solar or lunar calendar.
- Do not ask for leap-month status on the default input screen.
- Lunar input is treated as a regular lunar month by default.
- Leap-month handling is managed as an advanced option, internal policy, or support response.
- Many users may not know whether they were born in a leap month, so do not create conversion friction with a leap-month question.
- Birth time may be collected as `HH:mm` or unknown, but must be converted to `timeIndex` 0-12 before calling `iztro`.

## Required Wrapper Responsibilities

- [ ] Convert user input `HH:mm` to `iztro` `timeIndex` 0-12.
- [ ] Treat 00:00-00:59 as `timeIndex` 0, 조자시.
- [ ] Treat 23:00-23:59 as `timeIndex` 12, 야자시.
- [ ] Use `bySolar()` for solar input.
- [ ] Use `byLunar()` for lunar input.
- [ ] Treat lunar input as `isLeapMonth=false` by default.
- [ ] Explicitly use `fixLeap=true`.
- [ ] Explicitly set `yearDivide`, `horoscopeDivide`, `dayDivide`, and `algorithm`.
- [ ] Never expose `iztro` raw output directly to the user-facing screen.
- [ ] Use `formatZiweiForDisplay()` to create display JSON.
- [ ] Use `formatZiweiForPrompt()` to create AI prompt JSON.
- [ ] Use `formatZiweiMetadata()` to create storage/reproducibility metadata.

## MVP Default Calculation Config

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

- MVP starts with the `normal/normal` setting compatible with the `iztro` default behavior.
- If `yearDivide` or `horoscopeDivide` is changed to `exact`, results around 입춘 can change, including 오행국, 사화, and 대운 방향.
- Switching to `exact` should be decided later after comparing with external Ziwei sites.

## Required Stored Metadata

| Field | Required | Purpose |
|---|---|---|
| calendarType | yes | solar/lunar input distinction |
| solarDate | yes | final solar date used for calculation |
| lunarYear | if lunar or converted | lunar reproducibility |
| lunarMonth | if lunar or converted | lunar reproducibility |
| lunarDay | if lunar or converted | lunar reproducibility |
| isLeapMonth | yes | leap-month reproducibility |
| fixLeap | yes | leap-month correction policy reproducibility |
| timeIndex | yes | `iztro` time branch input |
| gender | yes | calculation input |
| iztroVersion | yes | engine version tracking |
| yearDivide | yes | year division setting |
| horoscopeDivide | yes | horoscope division setting |
| dayDivide | yes | late rat-hour setting |
| algorithm | yes | calculation algorithm setting |
| generatedAt | yes | generation timestamp |

## User-facing Display Policy

- Do not prominently expose 연간지, 년주, or “00년 출생” in the top screen.
- Use `chineseDate`, `rawDates`, and heavenly-stem/earthly-branch raw fields only for internal calculation or AI context.
- Do not prominently expose `lunarDate` because it may remain in Chinese-style characters.
- Main user-facing focus should be 명궁, 신궁, 재백궁, 관록궁, 부부궁, 대표 주성, and 사화 요약.
- Keep the full 12-palace details in a collapsed section.
- Raw terms such as 부처, 질액, 노복, 대한, 록, 권, 과, 기, 밤에 자시, 여자 must not remain in user-facing output.

## Minimum Display Sections

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

## Minimum Test Checklist for Arahan Service

- [ ] Solar input creates a `bySolar()` result.
- [ ] Lunar input creates a `byLunar()` result.
- [ ] General-date `bySolar` and `byLunar` results match when representing the same birth information.
- [ ] `HH:mm` is correctly converted to `timeIndex`.
- [ ] 23:00-23:59 is handled as 야자시.
- [ ] Lunar input uses `isLeapMonth=false` by default.
- [ ] `fixLeap=true` is stored.
- [ ] User-facing display JSON does not contain raw terms such as 부처, 질액, 노복, 대한, 록, 권, 과, 기, 밤에 자시, 여자.
- [ ] 연간지/년주/“00년 출생” text is not exposed in top summary or main cards.
- [ ] Formatter output uses 부부궁, 건강궁, 교우궁, 대운, 화록, 화권, 화과, 화기, 야자시, 여성.

## Implementation Reminder

- This document is not for modifying `iztro` source.
- Final implementation belongs in the Arahan service repository wrapper/formatter layer.
- The `iztro` fork should keep calculation verification and policy documents only.
- Actual service UI logic should be implemented in the Arahan repository.
