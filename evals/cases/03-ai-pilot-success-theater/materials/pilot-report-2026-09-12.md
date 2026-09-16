# Auto-Tagging Pilot — Results Summary

**Author:** ML team lead · **Date:** 2026-09-12 · **Pilot client:** Client A · **Pilot period:** 2026-07-01 → 2026-08-31

## Headline results
- **94.2% tagging accuracy** on evaluation set
- **120,480 product images auto-tagged** during the pilot
- **18 operations users onboarded** to the tagging review UI
- Average inference latency 340 ms/image; GPU cost ≈ $0.46 per 1,000 images

## Model
- Fine-tuned vision model on 38,000 labeled Client A catalog images (spring and summer lines).
- Evaluation set: 1,200 images sampled by the data team from the Client A spring catalog.
- 212 attribute tags across 14 categories (apparel, footwear, accessories, …).

## Recommendation
Pilot validated. Ready to scale to additional clients in Q4. Estimated onboarding per client: 3 weeks (labeling + fine-tuning).
