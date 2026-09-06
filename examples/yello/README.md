# Example: Yello onboarding pipeline

This example demonstrates Code Stacker with a real-world local-business publishing workflow.

```text
01_profile   → normalize the submitted business/website input
02_verify    → confirm the business against an external business source
03_detect    → determine profile/category/template selection
04_payment   → validate/debit credits
05_crawl     → run Crawl4AI and persist evidence
06_review    → review/normalize crawl evidence
07_enhance   → fill missing profile values using approved sources/AI
08_build     → fill a prebuilt hosting package template
09_edit      → pause for human edits/approval
10_publish   → publish the approved package
```

The workflow is intentionally linear. Each folder implements one business capability and returns only what the next pipeline step needs.

## Key separation

`04_payment` does not know how crawling works.

`05_crawl` does not know how payment was implemented; it receives an authorization reference.

`08_build` does not crawl the website. It consumes saved, reviewed profile/evidence references and fills the package template.

`10_publish` does not regenerate the package. It publishes an approved artifact.

## Example folder tree

```text
app/onboard/
├── 01_profile/
├── 02_verify/
├── 03_detect/
├── 04_payment/
├── 05_crawl/
├── 06_review/
├── 07_enhance/
├── 08_build/
├── 09_edit/
├── 10_publish/
├── pipeline.py
└── router.py
```

See [`pipeline.yaml`](pipeline.yaml) and [`05_crawl/FUNCTION.md`](05_crawl/FUNCTION.md).
