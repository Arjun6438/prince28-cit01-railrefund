# Dataset: RailRefund

Explains train ticket cancellation, refund and TDR rules and computes the exact refund for a ticket.

- **Start target:** Refund rules + IRCTC FAQs + 50 circulars
- **Full target:** + commercial circulars and concession rules (1,500+ pages)
- **Collected by:** `scripts/fetch.py` as `examate-student-bot` (https://github.com/Arjun6438/prince28-cit01-railrefund): robots.txt respected, at most 4 requests at once, 0.5 s between requests to one site.

## Where the documents come from

- https://contents.irctc.co.in/en/eticketCancel.html
- https://www.indianrail.gov.in/
- https://indianrailways.gov.in/

## Terms I follow

Public railway rules. Cite rule or circular number.

- Every answer links to the original document.
- No personal data is collected.

<!-- generated:start -->
## Sources (generated 2026-11-25 09:32 UTC by scripts/dataset_report.py)

| Source | Licence / terms | Files | MB | Pages* | How | Fetched | Example URL |
|---|---|---|---|---|---|---|---|
| indian_railways_ncr | Official railway content; internal research with attribution; obtain permission before republishing. | 1 | 0.1 | 5 | http | 2026-11-25 | https://ncr.indianrailways.gov.in/ministry/our-department/preview/218 |
| irctc | Official IRCTC content; no open licence verified. Internal research; obtain permission before republishing. | 7 | 2.3 | 45 | http | 2026-11-25 | https://contents.irctc.co.in/en/RefundCancellationRules.pdf |
| railway-board | Government of India publication; reuse licence not stated—verify terms before redistribution | 46 | 88.0 | 302 | http | 2026-11-25 | https://rb.indianrailways.gov.in/uploads/irpersonel/Vacancy_Circular/2025/CC%2023%20of%202025%20Bilingual.pdf |
| railway_board | Official Railway Board circular; cite circular number; obtain permission before republishing. | 2 | 0.9 | 4 | http | 2026-11-25 | https://rb.indianrailways.gov.in/uploads/directorate/traffic_comm/Comm_Cir_2024/CC%2001%20of%202024.pdf |

**Total:** 56 files, 91.3 MB, 356 pages. Not downloaded: 0 blocked by robots.txt, 3 failed.

*Pages = PDF pages, plus one page per 3,000 characters of text or HTML.

## Files (first 56 of 56)

The complete list, with a sha256 fingerprint of every file, is `data/manifest.jsonl`.

| # | Title / file | Source | Pages | Fetched | URL |
|---|---|---|---|---|---|
| 1 | North Central Railway Refund Rules | indian_railways_ncr | 5 | 2026-11-25 | https://ncr.indianrailways.gov.in/ministry/our-department/preview/218 |
| 2 | Cancellation and Refund Rules for IRCTC Trains | irctc | 4 | 2026-11-25 | https://contents.irctc.co.in/en/CancellationRulesforIRCTCTrain.pdf |
| 3 | Procedure for Cancellation of E-Tickets | irctc | 2 | 2026-11-25 | https://contents.irctc.co.in/en/eticketCancel.html |
| 4 | IRCTC E-Ticket FAQs | irctc | 2 | 2026-11-25 | https://contents.irctc.co.in/en/etktfaq.html |
| 5 | TDR Filing Steps | irctc | 5 | 2026-11-25 | https://contents.irctc.co.in/en/FileTDR.pdf |
| 6 | I-Ticket Cancellation | irctc | 1 | 2026-11-25 | https://contents.irctc.co.in/en/iticketcancel.html |
| 7 | Refund Rules and TDR Filing | irctc | 11 | 2026-11-25 | https://contents.irctc.co.in/en/RefundCancellationRules.pdf |
| 8 | IRCTC Terms and Conditions | irctc | 21 | 2026-11-25 | https://contents.irctc.co.in/en/userRegTermsAndConditions.html |
| 9 | CC-10 of 2024 – Time limit for advance reservations | railway-board | 2 | 2026-11-25 | https://rb.indianrailways.gov.in/uploads/directorate/traffic_comm/Comm_Cir_2024/2024-%20CC%20No_%2010%20of%202024-%20Change%20of%20Advance%20Reservation%20Period-ARP.pdf |
| 10 | CC-22 of 2025 – Allotment of Premium Brand Catering Outlet at station | railway-board | 2 | 2026-11-25 | https://rb.indianrailways.gov.in/uploads/directorate/traffic_comm/Comm_Cir_2025/Allotment%20of%20premium%20brand%20catering%20outlet.pdf |
| 11 | CC-01 of 2025 – Rationalization of nomenclature for persons with visual impairment | railway-board | 8 | 2026-11-25 | https://rb.indianrailways.gov.in/uploads/directorate/traffic_comm/Comm_Cir_2025/CC%2001%20of%202025.pdf |
| 12 | CC-02 of 2025 – Train demand module in PRIMES and ICMS | railway-board | 4 | 2026-11-25 | https://rb.indianrailways.gov.in/uploads/directorate/traffic_comm/Comm_Cir_2025/CC%2002%20of%202025.pdf |
| 13 | CC-03 of 2024 – Additional PMBJKs at railway stations | railway-board | 3 | 2026-11-25 | https://rb.indianrailways.gov.in/uploads/directorate/NFR/CC-03.pdf |
| 14 | CC-04 of 2024 – Review of STBA scheme revised vide CC-15 of 2023 | railway-board | 3 | 2026-11-25 | https://rb.indianrailways.gov.in/uploads/directorate/traffic_comm/Comm_Cir_2024/CC%2004%20of%202024.pdf |
| 15 | CC-05 of 2024 – Promotion of unreserved ticket booking through mobile | railway-board | 2 | 2026-11-25 | https://rb.indianrailways.gov.in/uploads/directorate/traffic_comm/Comm_Cir_2024/CC%2005%20of%202024.pdf |
| 16 | CC-06 of 2024 – Reserved accommodation in LSLRD coaches of fully reserved trains | railway-board | 2 | 2026-11-25 | https://rb.indianrailways.gov.in/uploads/directorate/traffic_comm/Comm_Cir_2024/CC%2006%20of%202024.pdf |
| 17 | CC-06 of 2025 – Replacement of terminology relating to persons with visual impairment | railway-board | 6 | 2026-11-25 | https://rb.indianrailways.gov.in/uploads/directorate/traffic_comm/Comm_Cir_2025/CC%2006%20of%202025%20%281%29.pdf |
| 18 | CC-07 of 2024 – Fare structure of Namo Bharat Rapid Rail | railway-board | 11 | 2026-11-25 | https://rb.indianrailways.gov.in/uploads/directorate/traffic_comm/Comm_Cir_2025/CC%2007%20of%202024%20%281%29.pdf |
| 19 | CC-08 of 2023 – Reduction in vinyl wrapping contract period | railway-board | 4 | 2026-11-25 | https://rb.indianrailways.gov.in/public/ourOrganization/public/pdf/CC_08_2023_VINYL_WRAPPING_703ace15_1761966284192.PDF |
| 20 | CC-08 of 2025 – Modification in Tatkal scheme | railway-board | 6 | 2026-11-25 | https://rb.indianrailways.gov.in/uploads/directorate/traffic_comm/Comm_Cir_2025/CC%2008%20of%202025.pdf |
| 21 | CC-09 of 2023 – One Station One Product scheme modifications | railway-board | 3 | 2026-11-25 | https://rb.indianrailways.gov.in/public/ourOrganization/public/pdf/CC%20%2009%20of%202023_38b35a7d_1761966284191.pdf |
| 22 | CC-09 of 2025 – Pilot project for engagement of M-UTS Sahayaks | railway-board | 23 | 2026-11-25 | https://rb.indianrailways.gov.in/uploads/directorate/traffic_comm/Comm_Cir_2025/CC%2009%20of%2025.pdf |
| 23 | CC-10 of 2023 – Revised ATM and Digital Banking Units policy | railway-board | 6 | 2026-11-25 | https://rb.indianrailways.gov.in/public/ourOrganization/public/pdf/CC_10_Revised%20ATM%20policy_24eaf352_1761966284190.pdf |
| 24 | CC-11 of 2023 – Discount scheme in AC Chair Car and Executive classes | railway-board | 3 | 2026-11-25 | https://rb.indianrailways.gov.in/public/ourOrganization/public/pdf/CC%2011%20of%202023_52430039_1761966284189.pdf |
| 25 | CC-11 of 2025 – Rationalization of basic fare for passenger train services w.e.f. 01.07.2025 | railway-board | 61 | 2026-11-25 | https://rb.indianrailways.gov.in/uploads/directorate/traffic_comm/Comm_Cir_2025/CC%2011%20of%202025.pdf |
| 26 | CC-13 of 2023 – Time limit for collection of refund on tickets cancelled through 139 or IRCTC | railway-board | 2 | 2026-11-25 | https://rb.indianrailways.gov.in/public/ourOrganization/public/pdf/CC%2013%20of%202023_fb53c872_1761966270675.pdf |
| 27 | CC-13 of 2024 – Clarification and modifications in ATM/DBU policy | railway-board | 2 | 2026-11-25 | https://rb.indianrailways.gov.in/uploads/directorate/traffic_comm/Comm_Cir_2024/CC%2013%20of%202024%20dated%2026_12_2024%20regarding%20ClarificationModification%20in%20ATMDBU%20policy%20%281%29.pdf |
| 28 | CC-13 of 2025 – Standardized identity cards for catering staff and vendors | railway-board | 3 | 2026-11-25 | https://rb.indianrailways.gov.in/uploads/directorate/traffic_comm/Comm_Cir_2025/CC%2013%20of%202025.pdf |
| 29 | CC-14 of 2023 – Promotion of unreserved ticket booking through mobile | railway-board | 2 | 2026-11-25 | https://rb.indianrailways.gov.in/public/ourOrganization/public/pdf/CC-14_e9c6e5e3_1761966270674.pdf |
| 30 | CC-14 of 2025 – Discounted round-trip package for festival rush | railway-board | 3 | 2026-11-25 | https://rb.indianrailways.gov.in/uploads/directorate/traffic_comm/Comm_Cir_2025/CC%2014%20of%202025.pdf |
| 31 | CC-15 of 2023 – Review of Station Ticket Booking Agent scheme | railway-board | 4 | 2026-11-25 | https://rb.indianrailways.gov.in/public/ourOrganization/public/pdf/CC%2015%20of%202023_Bilingual_b1f1a991_1761966270673.pdf |
| 32 | CC-15 of 2025 – Booking of reserved tickets online | railway-board | 2 | 2026-11-25 | https://rb.indianrailways.gov.in/uploads/directorate/traffic_comm/Comm_Cir_2025/CC%2015%20of%202025.pdf |
| 33 | CC-16 of 2023 – Quoting of commission by Station Ticket Booking Agent | railway-board | 3 | 2026-11-25 | https://rb.indianrailways.gov.in/public/ourOrganization/public/pdf/CC%2016%20of%202023_Bilingual_e6c06ae7_1761966270671.pdf |
| 34 | CC-16 of 2025 – Revision of travelling and daily allowance rates for Railway Users’ Consultative Committees | railway-board | 2 | 2026-11-25 | https://rb.indianrailways.gov.in/uploads/directorate/traffic_comm/Comm_Cir_2025/CC%2016%20of%2025%20%281%29.pdf |
| 35 | CC-17 of 2025 – Downgrading of sleeper class coaches of special trains | railway-board | 2 | 2026-11-25 | https://rb.indianrailways.gov.in/uploads/directorate/traffic_comm/Comm_Cir_2025/CC%2017%20of%202025.pdf |
| 36 | CC-18 of 2023 – Automatic refund of fare difference for e-ticket passengers in lower-class accommodation | railway-board | 2 | 2026-11-25 | https://rb.indianrailways.gov.in/public/ourOrganization/public/pdf/CC%2018%20of%202023_272f02e7_1761966270668.pdf |
| 37 | CC-01A of 2024 – Reservation quota for persons with disabilities in 2S/CC | railway-board | 7 | 2026-11-25 | https://rb.indianrailways.gov.in/uploads/directorate/traffic_comm/Comm-Cir-2024/CC%201A%20of%202024.pdf |
| 38 | CC-20 of 2023 – Fare of Vistadome coaches in Tejas and Gatimaan trains | railway-board | 2 | 2026-11-25 | https://rb.indianrailways.gov.in/public/ourOrganization/public/pdf/CC%2020%20of%202023_55469cba_1761966270667.pdf |
| 39 | CC-22 of 2023 – Fare structure of Heritage Special Train | railway-board | 2 | 2026-11-25 | https://rb.indianrailways.gov.in/public/ourOrganization/public/pdf/CC%2022%20of%202023_83d86df1_1761966248024.pdf |
| 40 | CC-23 of 2025 – Extension of Magisterial Scheme for check and prevention of ticketless travel | railway-board | 2 | 2026-11-25 | https://rb.indianrailways.gov.in/uploads/irpersonel/Vacancy_Circular/2025/CC%2023%20of%202025%20Bilingual.pdf |
| 41 | CC-24 of 2025 – Rationalization of basic fare for passenger train services w.e.f. 26.12.2025 | railway-board | 61 | 2026-11-25 | https://rb.indianrailways.gov.in/uploads/directorate/traffic_comm/Comm_Cir_2025/CC_24_2025.pdf |
| 42 | CC-27 of 2023 – Review of Value Added Tour scheme | railway-board | 2 | 2026-11-25 | https://rb.indianrailways.gov.in/public/ourOrganization/public/pdf/CC%2027%20of%202023_a5339097_1761966248021.pdf |
| 43 | CC-28 of 2023 – Season tickets in AC EMU/DEMU/MEMU | railway-board | 2 | 2026-11-25 | https://rb.indianrailways.gov.in/public/ourOrganization/public/pdf/CC%2028%20of%202023_73e227e9_1761966248020.pdf |
| 44 | CC-31 of 2023 – Fare structure of Amrit Bharat trains | railway-board | 3 | 2026-11-25 | https://rb.indianrailways.gov.in/public/ourOrganization/public/pdf/CC%2031%20of%202023_30bd5c70_1761966248019.pdf |
| 45 | CC-32 of 2023 – Operation of Aastha special trains | railway-board | 2 | 2026-11-25 | https://rb.indianrailways.gov.in/public/ourOrganization/public/pdf/CC%2032%20of%202023_ccaa9d7c_1761966248018.pdf |
| 46 | CC-33 of 2023 – Catering facility in coaches/trains booked on full tariff rates | railway-board | 3 | 2026-11-25 | https://rb.indianrailways.gov.in/public/ourOrganization/public/pdf/CC%2033%20_c0977d08_1761966248017.pdf |
| 47 | CC-07 of 2025 – Scheme for upgradation of waiting list passengers | railway-board | 5 | 2026-11-25 | https://rb.indianrailways.gov.in/uploads/directorate/traffic_comm/Comm_Cir_2025/cc07.pdf |
| 48 | CC-23 of 2023 – Reserved accommodation in LSLRD coaches | railway-board | 1 | 2026-11-25 | https://rb.indianrailways.gov.in/public/ourOrganization/public/pdf/Commercial%20Circular%20no_%2023%20of%202023_9270120d_1761966248023.pdf |
| 49 | CC-19 of 2025 – Earmarking of accommodation for on-board staff in trains | railway-board | 3 | 2026-11-25 | https://rb.indianrailways.gov.in/uploads/directorate/traffic_comm/Comm_Cir_2025/document%20-%202026-01-13T125851_328.pdf |
| 50 | CC-09 of 2024 – Additional Pradhan Mantri Bhartiya Janaushadhi Kendras at stations | railway-board | 2 | 2026-11-25 | https://rb.indianrailways.gov.in/uploads/directorate/traffic_comm/Comm_Cir_2024/Letter%20to%20Zonal%20Railways%20dated%2009_10_2024%20regarding%20additional%2039%20PMBJK%20locations%20%282%29.pdf |
| 51 | CC-24 of 2023 – Management of mobile catering services | railway-board | 20 | 2026-11-25 | https://rb.indianrailways.gov.in/public/ourOrganization/public/pdf/Management%20of%20Mobile%20Catering%20Services-%20Addendum%20to%20Catering%20Policy%202017_24600648_1761966248022.pdf |
| 52 | CC-18 of 2025 – MRP of Rail Neer and shortlisted packaged drinking water | railway-board | 2 | 2026-11-25 | https://rb.indianrailways.gov.in/uploads/directorate/traffic_comm/Comm_Cir_2025/MRP%20of%20Rail%20Neer%20Shortlisted%20packaged%20drinking%20water%20.pdf |
| 53 | CC-10 of 2025 – Preparation of first reservation charts | railway-board | 2 | 2026-11-25 | https://rb.indianrailways.gov.in/uploads/directorate/traffic_comm/Comm_Cir_2025/MX-M453N_20250708_161959.pdf |
| 54 | CC-04 of 2025 – Commercial Circular No. 04 of 2025 | railway-board | 5 | 2026-11-25 | https://rb.indianrailways.gov.in/uploads/directorate/traffic_comm/Comm_Cir_2025/MX-M453N_20250710_201749.pdf |
| 55 | Commercial Circular 01 of 2024 | railway_board | 2 | 2026-11-25 | https://rb.indianrailways.gov.in/uploads/directorate/traffic_comm/Comm_Cir_2024/CC%2001%20of%202024.pdf |
| 56 | Corrigendum to Refund Rules | railway_board | 2 | 2026-11-25 | https://rb.indianrailways.gov.in/uploads/directorate/Corrigendum_Refund_Rule.pdf |
<!-- generated:end -->

## Known gaps

1. **Circular collection completed:** All 50 targeted Railway Board circular PDFs have been downloaded successfully. However, their uniqueness, circular numbers, and relevance to refund-related rules still need verification.

2. **Refund-related coverage:** Some collected circulars cover topics unrelated to refunds, cancellations, and TDR. More targeted refund and cancellation circulars may be needed.

3. **Concession rules:** Concession rules and eligibility conditions have not yet been collected comprehensively.

4. **Dataset size:** The initial collection is complete, but the full target of 1,500+ pages has not yet been reached.

5. **Licensing verification:** The reuse and redistribution terms of each downloaded document have not been individually verified.

6. **Document validation:** All downloaded PDFs still need validation for text extraction, duplicate content, page counts, circular numbers, and effective dates.

7. **Data freshness:** Railway refund rules and circulars may change. The dataset needs periodic updates to ensure that answers use the latest applicable rules.
