# Horse Breeding & Genetics Guide

Complete guide to horse breeding mechanics, genetics, and inheritance probabilities in Story of Alicia.

---

## Table of Contents

- [Overview](#overview)
- [Pregnancy System](#pregnancy-system)
- [Foal Grade Calculation](#foal-grade-calculation)
- [Coat Inheritance System](#coat-inheritance-system)
- [Breeding Bonuses](#breeding-bonuses)
- [Appearance Genetics](#appearance-genetics)
- [Stat Inheritance](#stat-inheritance)
- [Combo System](#combo-system)
- [Breeding Strategy Guide](#breeding-strategy-guide)
- [Example Calculations](#example-calculations)

---

## Overview

Horse breeding allows players to produce foals by pairing a **mare** (mother) with a **stallion** (father). The foal's characteristics are determined through a complex genetics system that considers:

- Parent grades and stats
- Coat colors and rarity
- Physical appearance traits
- Breeding performance (combos)
- Stallion freshness (pregnancy chance)
- Breeding bonuses
- Random variation

---

## Pregnancy System

### Pregnancy Chance

The stallion's **pregnancy chance** is determined by how many times it has been used for breeding (lifetime count):

```
Pregnancy Chance = min(timesBreeded, 30)
```

This value affects the **breeding success rate**:

| Times Bred | Pregnancy Chance | Success Rate | Hearts (UI) |
|-----------|------------------|--------------|-------------|
| 0         | 0                | 64%          | 3.2 ♥♥♥     |
| 5         | 5                | 54%          | 2.7 ♥♥♥     |
| 10        | 10               | 44%          | 2.2 ♥♥      |
| 15        | 15               | 34%          | 1.7 ♥♥      |
| 20        | 20               | 24%          | 1.2 ♥       |
| 25        | 25               | 14%          | 0.7 ♥       |
| 30+       | 30               | 2%           | 0.1 ♥       |

**Formula:**
```
Base Success Rate = 64%
Success Rate = Base - (Pregnancy Chance × 2%)
```

### Breeding Failure

If breeding fails (pregnancy doesn't occur):
- The **mare's combo resets to 0**
- The stallion's `timesBreeded` still increments
- No foal is produced
- Breeding fee is still consumed

---

## Foal Grade Calculation

The foal's **grade** (1-8) is calculated using a weighted probability system based on the parent grades.

### Base Grade Probabilities

The system uses grade difference to determine probabilities:

```
Grade Difference = |mareGrade - stallionGrade|
```

**Probability Distribution:**

| Grade Diff | Foal Grade Distribution |
|-----------|-------------------------|
| 0         | Heavy weight on parent grade |
| 1-2       | Spread around parent grades |
| 3+        | Wide spread, lower average |

### Fertility Peak Bonus

**Breeding Bonus Type 1** (Fertility Peak) can boost high-grade probabilities:

| Bonus ID | Fertility Level | Effect |
|----------|----------------|---------|
| 11       | Level 1        | Small shift to higher grades |
| 12       | Level 2        | Medium shift to higher grades |
| 13       | Level 3        | Large shift to higher grades |

**Mechanism:** 
Fertility Peak redistributes probability from lower grades to higher grades. For example, Level 3 might shift 30% of grade 5 probability to grade 6, and 30% of grade 6 to grade 7.

### Grade Examples

**Example 1:** Grade 6 Mare × Grade 6 Stallion
- High probability: Grade 6 (~40%)
- Medium probability: Grade 5, 7 (~25% each)
- Low probability: Grade 4, 8 (~5% each)

**Example 2:** Grade 4 Mare × Grade 7 Stallion (diff = 3)
- Spread across grades 4-7
- Average result: ~Grade 5-6

---

## Coat Inheritance System

### How Coat Inheritance Works

The foal's **coat color** is determined by weighted random selection from:
1. **Mare's coat**
2. **Stallion's coat** (boosted by inheritance bonuses)
3. **Grandparents' coats** (up to 4)
4. **Random coat** (valid for foal's grade)

### Base Inheritance Weights

```
Mare:        10
Stallion:    10 × coatInheritanceRate × bonusMultiplier
Each GP:     5
Random:      60 (flexible, fills remainder)
```

All weights are normalized to sum to 100%.

### Coat Inheritance Rates

Each coat has a base **inheritance rate** that affects its probability:

| Coat Name | Base Rate | Rarity | Min Grade |
|-----------|-----------|--------|-----------|
| Chestnut | 30.0 | Common | 1 |
| Bay | 25.0 | Common | 1 |
| Champagne Sabino | 30.0 | Common | 1 |
| White-Grey | 25.0 | Common | 2 |
| Liver Chestnut | 21.0 | Common | 3 |
| Black Pinto | 19.0 | Uncommon | 7 |
| **Black** | **7.0** | **Rare** | **7** |
| Cremello | 3.0 | Rare | 8 |

*Lower rates = rarer coats = harder to inherit*

### Inheritance Rate Bonus

The **inheritance rate bonus** boosts the stallion's coat probability. It is calculated from:

**Formula:**
```
Combo Bonus = (mareCombo + stallionCombo) × 1%
Pregnancy Bonus = (30 - pregnancyChance) = (30 - timesBreeded)
Lineage Bonus = 0 (not yet implemented)

Total Bonus = Combo + Pregnancy + Lineage (capped at 100%)
Bonus Multiplier = 1.0 + (Total Bonus / 100)
```

**Key Points:**
- ✅ **Mare's combo helps pass stallion's coat** - Rewards skilled breeding
- ✅ **Fresh stallions** (0 uses) = +30% bonus
- ✅ **Used stallions** (30 uses) = +0% bonus
- ✅ **All bonuses boost only the stallion's coat**, not the mare's

### UI Display (0-8 Scale)

The inheritance rate shown in the breeding market represents the **estimated probability** of getting the stallion's coat:

```
Stallion Weight = 10 × coatRate × bonusMultiplier
Estimated Probability = stallionWeight / (90 + stallionWeight) × 100%
Arrow UI Display (0-8) = estimatedProbability / 12.5%
```

### Grade Restrictions

If a parent or grandparent coat requires a higher grade than the foal has, a random coat (valid for the foal's grade) is selected instead using weighted selection based on coat inheritance rates.

---

## Breeding Bonuses

### Bonus Activation

Breeding bonuses are rolled **every breeding attempt** based on the stallion's grade:

**Grade Categories:**
- **Small Grade:** 4-6
- **Big Grade:** 7-8

**Activation Probability:**
- Small Grade: **10%** chance
- Big Grade: **15%** chance

### Bonus Types

**Type 0: Pregnancy Chance Increase**

Increases the breeding success rate:

| Bonus ID | Effect | Probability Weight (Small) | Probability Weight (Big) |
|----------|--------|---------------------------|-------------------------|
| 1 | +5% success | 30 | 1 |
| 2 | +10% success | 20 | 5 |
| 3 | +15% success | 10 | 10 |

**Type 1: Fertility Peak**

Boosts probability of high-grade foals:

| Bonus ID | Effect | Probability Weight (Small) | Probability Weight (Big) |
|----------|--------|---------------------------|-------------------------|
| 11 | Fertility Level 1 | 5 | 10 |
| 12 | Fertility Level 2 | 3 | 20 |
| 13 | Fertility Level 3 | 1 | 30 |

**Selection Process:**
1. Check if bonus activates (10% or 15%)
2. If activated, randomly select bonus based on weights
3. Apply bonus effect to breeding

---

## Appearance Genetics

### Physical Traits

The foal inherits physical measurements from both parents with variation:

**Traits:**
- `scale` (overall size)
- `legLength`
- `legVolume`
- `bodyLength`
- `bodyVolume`

**Formula:**
```
Average = (mareValue + stallionValue) / 2
Variation = random(-20%, +20%)
FoalValue = clamp(Average × (1 + Variation), 1, 10)
```

**Example:**
- Mare bodyLength: 7
- Stallion bodyLength: 9
- Average: 8
- Variation: +15%
- Foal bodyLength: 8 × 1.15 = 9.2 → clamped to **9**

### Face Inheritance

Simple 50/50 random selection:
```
Foal Face = random choice(mare face, stallion face)
```

### Mane and Tail

Inherits from parents with shape/color restrictions based on:
- Foal's grade (shape restrictions)
- Foal's coat color (color restrictions)
- Weighted random selection based on inheritance rates

---

## Stat Inheritance

### How Stats Are Inherited

The foal's **stats** (Agility, Courage, Rush, Endurance, Ambition) are calculated from parent stats and adjusted to fit the foal's grade.

### Base Stat Calculation

Each stat is calculated individually:

**Formula:**
```
Average Stat = (Mare Stat + Stallion Stat) / 2
```

**Then, the variation method depends on whether the average is above or below 2:**

#### Normal Stats (Average > 2)

For stats where the parent average is **above 2**, apply random variation:

```
Variation = random(-3, +3)
Final Stat = Average + Variation (clamped to 0-100)
```

**Example:**
- Mare Agility: 15
- Stallion Agility: 21
- Average: 18
- Variation: random(-3, +3) = +2
- Foal Agility: 18 + 2 = **20**

#### Low Stats (Average ≤ 2)

For stats where the parent average is **2 or below**, a different system applies - instead of ±3 variation, a **weighted mutation bonus** is added on top of the average:

```
Weighted Bonus = random with probabilities:
  - +0: 30% chance
  - +1: 40% chance (most common)
  - +2: 20% chance
  - +3: 10% chance

Final Stat = Average + Weighted Bonus
```

**Example 1: Both Parents Have 0 Agility**
- Average: 0
- Possible outcomes:
  - 0 + 0 = **0** (30% chance)
  - 0 + 1 = **1** (40% chance)
  - 0 + 2 = **2** (20% chance)
  - 0 + 3 = **3** (10% chance)
- Expected average: **~1.1**

**Example 2: Mare 1, Stallion 3 Rush**
- Average: 2
- Possible outcomes:
  - 2 + 0 = **2** (30% chance)
  - 2 + 1 = **3** (40% chance)
  - 2 + 2 = **4** (20% chance)
  - 2 + 3 = **5** (10% chance)
- Expected average: **~3.1**

**Why This System?**
- ✅ Prevents complete genetic stagnation for low-stat horses
- ✅ Still bases inheritance on parent stats (not completely random)
- ✅ Allows gradual improvement through selective breeding
- ✅ Most common result is +1 (40%), keeping it predictable
- ✅ Rare chance (+3 at 10%) for significant improvement

### Grade-Based Stat Total

After calculating individual stats, they are **proportionally adjusted** to fit the foal's grade:

**Target Stat Total by Grade:**

| Grade | Min Total | Max Total |
|-------|-----------|-----------|
| 1 | 0 | 9 |
| 2 | 10 | 19 |
| 3 | 20 | 29 |
| 4 | 30 | 39 |
| 5 | 40 | 49 |
| 6 | 50 | 59 |
| 7 | 60 | 69 |
| 8 | 70 | 79 |

**Process:**
1. Calculate base stats from parents (with variation/mutation)
2. Sum all five stats
3. Pick a random target total within the grade's range
4. Scale all stats proportionally to hit the target
5. Adjust the largest stat if needed to fix rounding errors

**Example:**
- Foal Grade: 6
- Base stats from parents: Agility 18, Courage 15, Rush 12, Endurance 10, Ambition 8
- Current total: 63
- Target range: 50-59
- Target rolled: 54
- Scale factor: 54/63 = 0.857
- Final stats: Agility 15, Courage 13, Rush 10, Endurance 9, Ambition 7
- **Total: 54** ✓

### Strategic Implications

**For High-Stat Breeding:**
- ✅ Use parents with high stats in desired areas
- ✅ Higher grade foals = higher total stats
- ✅ Stats will scale to fit grade (can go up OR down)
- ⚠️ Even perfect parents can produce varied results due to ±3 variation

**For Low-Stat Recovery:**
- ✅ Low stats (≤2) have mutation system for improvement
- ✅ 70% chance to improve (40% +1, 20% +2, 10% +3)
- ✅ Build stats gradually over generations
- ⚠️ Can't rely on single generation to fix very low stats

---

## Combo System

### Mare Combo

The mare's **breeding combo** tracks consecutive successful breedings:

**Increment:** `breedingCombo + 1` on successful breeding
**Reset:** `breedingCombo = 0` on breeding failure

**Benefits:**
- Contributes to inheritance rate bonus (1% per combo)
- Rewards consistent breeding success
- Stored per mare (each mare has independent combo)

**Example:**
- Mare with 20 combo = **+20%** to inheritance bonus
- If breeding fails, combo resets to 0
- Must rebuild combo from scratch

### Stallion Combo

Currently tracked but **not incremented** (always 0).

**Placeholder for future:**
- Could increment on successful breedings
- Would add to inheritance bonus
- Rewards high-quality stallions

---

## Breeding Strategy Guide

### Maximizing Coat Inheritance

**Goal:** Get the stallion's rare coat (e.g., Black)

**Strategy:**
1. ✅ Use a **high-combo mare** (20+ combo)
2. ✅ Choose a **fresh stallion** (0-5 uses) for +25-30% pregnancy bonus
3. ✅ The stallion must have the **desired rare coat**
4. ✅ Mare's coat doesn't matter (bonus helps stallion only)
5. ✅ Build mare's combo by breeding successfully multiple times

**Expected Results:**
- Black coat (base 7.0) with 50% bonus = ~24% chance (vs ~7% base)
- Common coat (base 30.0) with 50% bonus = ~50% chance (vs ~30% base)

### Maximizing Foal Grade

**Goal:** Get a Grade 8 foal

**Strategy:**
1. ✅ Use **Grade 7-8 parents** (higher grade = better odds)
2. ✅ Breed during **Fertility Peak bonus** (Type 1, ID 13)
3. ✅ Grade 7+ stallions have 15% bonus activation chance
4. ✅ Parents with **same grade** = more focused distribution
5. ⚠️ Grade difference 3+ = lower average grade

**Expected Results:**
- G7 × G7 with Fertility Peak 3 = ~30-40% chance of G8
- G6 × G8 = ~15-20% chance of G8
- G5 × G5 = ~2-5% chance of G8

### Balancing Pregnancy Success

**Trade-off:** Fresh stallions have better inheritance but cost more

| Stallion Uses | Success Rate | Inheritance Bonus | Value |
|---------------|--------------|-------------------|-------|
| 0-5 | 50-64% | +25-30% | ⭐⭐⭐ Premium |
| 10-15 | 34-44% | +15-20% | ⭐⭐ Good |
| 20-25 | 14-24% | +5-10% | ⭐ Budget |
| 30+ | 2% | +0% | ❌ Avoid |

**Recommendation:**
- For **rare coats**: Use fresh stallions (0-10 uses)
- For **common coats**: 15-20 uses is acceptable
- Avoid stallions with 25+ uses (very low success rate)

### Building Mare Combo

**Goal:** Reach 20+ combo for maximum bonus

**Strategy:**
1. Start with a **Grade 6+ mare** (better success rates)
2. Use **high-success stallions** (0-15 uses, 30-60% success)
3. Breed **consistently** - each success builds combo
4. Avoid **risky breedings** that might break combo
5. Consider using **pregnancy bonus items** to protect combo

**Combo Value:**
- 0 combo = +0%
- 10 combo = +10%
- 20 combo = +20%
- 50 combo = +50% (capped at 100% total bonus)

### Recovering Low Stats

**Goal:** Improve horses with very low stats (≤2 in some areas)

**Strategy:**
1. ✅ Understand the **mutation system** kicks in for stats ≤2
2. ✅ Expect **gradual improvement** over multiple generations
3. ✅ Breed low-stat horses together to trigger mutation bonuses
4. ✅ Each generation has 70% chance to improve (40% +1, 20% +2, 10% +3)
5. ⚠️ Don't expect single-generation fixes

**Example Progression:**
- **Generation 1**: Both parents Agility 0 → Foal avg 0
  - Mutation applies: 40% chance = Agility 1
- **Generation 2**: Breed two Agility 1 horses → Foal avg 1
  - Mutation applies: 40% chance = Agility 2
- **Generation 3**: Breed two Agility 2 horses → Foal avg 2
  - Mutation applies: 40% chance = Agility 3
- **Generation 4**: Agility 3 × Agility 4 → Foal avg 3.5
  - Normal variation: 3.5 ± 3 = 0-6 range
  - Average result: ~4

**Expected Timeline:**
- Agility 0 → 3: Approximately **3-5 generations**
- Agility 0 → 10: Approximately **8-12 generations**
- Each generation requires successful pregnancy (30-60% chance)

**Tips:**
- ✅ Focus on one stat at a time (easier to track)
- ✅ Use Grade 6+ horses (better pregnancy rates)
- ✅ Be patient - genetic recovery takes time
- ⚠️ Once stats exceed 2, mutation stops (normal ±3 variation applies)

---

## Example Calculations

### Example 1: Common Coat Breeding

**Setup:**
- Mare: Bay (rate 25.0), Grade 6, 15 combo
- Stallion: Chestnut (rate 30.0), Grade 6, 10 uses, 0 combo
- 4 Grandparents available

**Bonus Calculation:**
```
Combo: (15 + 0) = 15%
Pregnancy: (30 - 10) = 20%
Total: 35%
Multiplier: 1.35
```

**Weights:**
```
Mare: 10 × 25.0 = 250
Stallion: 10 × 30.0 × 1.35 = 405
GPs: 5 × 4 = 20
Random: 60
Total: 735
```

**Normalized Probabilities:**
- Mare (Bay): 250/735 = **34.0%**
- Stallion (Chestnut): 405/735 = **55.1%** ⭐
- GPs (4 total): 20/735 = **2.7%** (0.7% each)
- Random: 60/735 = **8.2%**

**UI Display:**
```
Estimated: 405 / (90 + 405) × 100 = 81.8%
UI: 81.8 / 12.5 = 6.5 → Shows 6/8 ⭐⭐⭐⭐⭐⭐
```

**Result:** Very high chance (~55%) of getting the stallion's Chestnut coat!

---

### Example 2: Rare Black Coat Breeding

**Setup:**
- Mare: White-Grey (rate 25.0), Grade 7, 25 combo
- Stallion: Black (rate 7.0), Grade 7, 0 uses (fresh), 0 combo
- 2 Grandparents available

**Bonus Calculation:**
```
Combo: (25 + 0) = 25%
Pregnancy: (30 - 0) = 30%
Total: 55%
Multiplier: 1.55
```

**Weights:**
```
Mare: 10 × 25.0 = 250
Stallion: 10 × 7.0 × 1.55 = 108.5
GPs: 5 × 2 = 10
Random: 60
Total: 428.5
```

**Normalized Probabilities:**
- Mare (White-Grey): 250/428.5 = **58.3%**
- Stallion (Black): 108.5/428.5 = **25.3%** ⭐
- GPs (2 total): 10/428.5 = **2.3%** (1.2% each)
- Random: 60/428.5 = **14.0%**

**UI Display:**
```
Estimated: 108.5 / (90 + 108.5) × 100 = 54.6%
UI: 54.6 / 12.5 = 4.4 → Shows 4/8 ⭐⭐⭐⭐
```

**Result:** Despite Black being rare (base 7.0), the 55% bonus boosts it to **25.3%** chance (vs ~7% without bonuses)!

---

### Example 3: Maximum Optimization

**Setup:**
- Mare: Chestnut (rate 30.0), Grade 8, 50 combo
- Stallion: Chestnut (rate 30.0), Grade 8, 0 uses, 0 combo
- 4 Grandparents, all common coats

**Bonus Calculation:**
```
Combo: (50 + 0) = 50%
Pregnancy: (30 - 0) = 30%
Total: 80% (capped at 100%, but 80% is fine)
Multiplier: 1.80
```

**Weights:**
```
Mare: 10 × 30.0 = 300
Stallion: 10 × 30.0 × 1.8 = 540
GPs: 5 × 4 = 20
Random: 60
Total: 920
```

**Normalized Probabilities:**
- Mare (Chestnut): 300/920 = **32.6%**
- Stallion (Chestnut): 540/920 = **58.7%** ⭐⭐⭐
- GPs (4 total): 20/920 = **2.2%** (0.5% each)
- Random: 60/920 = **6.5%**

**UI Display:**
```
Estimated: 540 / (90 + 540) × 100 = 85.7%
UI: 85.7 / 12.5 = 6.9 → Shows 6/8 or 7/8 ⭐⭐⭐⭐⭐⭐⭐
```

**Result:** Almost 60% chance of getting the stallion's coat - near maximum optimization!

---

### Example 4: Used Stallion

**Setup:**
- Mare: Bay (rate 25.0), Grade 5, 5 combo
- Stallion: Black (rate 7.0), Grade 7, 30 uses (exhausted), 0 combo

**Bonus Calculation:**
```
Combo: (5 + 0) = 5%
Pregnancy: (30 - 30) = 0%
Total: 5%
Multiplier: 1.05
```

**Weights:**
```
Mare: 10 × 25.0 = 250
Stallion: 10 × 7.0 × 1.05 = 73.5
GPs: 20
Random: 60
Total: 403.5
```

**Normalized Probabilities:**
- Mare (Bay): 250/403.5 = **62.0%**
- Stallion (Black): 73.5/403.5 = **18.2%**
- GPs: 20/403.5 = **5.0%**
- Random: 60/403.5 = **14.9%**

**Pregnancy Success Rate:**
```
Base 64% - (30 × 2%) = 4% success rate
```

**Result:** Only **4% chance of pregnancy** and **18% chance of Black coat** if successful. Very inefficient!

---

## Technical Details

### Data Storage

**Character Fields:**
- `horseSlotCount`: Number of horse slots owned
- `breedingMoneySpent`: Total money spent on breeding

**Horse Fields:**
```cpp
struct Breeding {
  uint32_t timesBreeded;  // Lifetime breeding count
  uint8_t breedingCombo;  // Consecutive successful breedings (mare only)
}
```

**Breeding Market:**
- Stallions listed with price, expiration time
- Owner UID tracked
- Pregnancy chance and inheritance rate displayed

### Configuration

**From `libconfig_c.dat`:**
- `InheritanceRateBonusUnit = 2` (legacy, now using 1% in code)
- `DNA_SkinInfo`: Coat data (name, inheritance rate, grade requirement, rarity)
- `BonusProbInfo`: Breeding bonus probabilities and effects

---

## Tips & Tricks

### For Mare Owners

1. **Build combo early** with reliable stallions (10-15 uses, 40-50% success)
2. **Protect your combo** - avoid risky low-success breedings
3. **Use high combo for rare coats** - 20+ combo significantly boosts rare coat chances
4. **Grade matters less** for combo building - even G5 mares can reach high combos

### For Stallion Owners

1. **Price fresh stallions higher** - They're worth 2-3x more (30% bonus)
2. **Market based on coat rarity** - Rare coats (Black, Cremello) command premium prices
3. **Retire at 25-30 uses** - Below 10% success rate = poor value
4. **Grade 7-8 stallions** attract more buyers (15% bonus activation)

### For Breeders

1. **Check inheritance rate** - 4/8+ is good, 6/8+ is excellent
2. **Match your goals** - High combo mare + fresh rare stallion = best rare coat odds
3. **Be patient** - Rare coats are rare for a reason (~25% even with perfect setup)
4. **Track your combos** - One failure resets everything
5. **Consider grade vs coat** - Sometimes a G6 with perfect coat > G7 with wrong coat

---

## Frequently Asked Questions

### Q: Does the mare's coat affect inheritance?
**A:** Yes, the mare has a base 10% × her coat's inheritance rate probability. However, bonuses only boost the **stallion's** coat.

### Q: Can I get both parents' coats mixed?
**A:** No, the foal inherits **one complete coat** from mare, stallion, grandparents, or random pool.

### Q: What happens if combo is broken?
**A:** The mare's `breedingCombo` resets to 0 and must be rebuilt from scratch. This happens on any breeding failure (pregnancy didn't occur).

### Q: Is there a maximum combo?
**A:** Combo can go above 100, but the total bonus is capped at 100% (multiplier = 2.0×).

### Q: Do stallion combos work?
**A:** Currently, stallion combos are tracked but not incremented (always 0). This is a placeholder for future features.

### Q: What's the best breeding strategy?
**A:** High-combo mare (20+) + fresh rare stallion (0-5 uses) = ~25-30% chance of rare coat vs ~7% baseline.

### Q: How many breeding attempts for a rare coat?
**A:** With optimal setup (~25% chance per attempt), expect **4-6 successful pregnancies** on average to get the rare coat. Account for breeding failures too (success rate 30-60%).

### Q: Can low-stat parents produce better foals?
**A:** Yes! When a stat's parent average is ≤2, a **mutation system** applies that adds a weighted random bonus (+0 to +3) on top of the average. This gives a 70% chance to improve (40% for +1, 20% for +2, 10% for +3). However, this is gradual - don't expect a single generation to fix very low stats.

### Q: Why did my foal get worse stats than the parents?
**A:** Stats are **scaled to fit the foal's grade**. If your foal is a lower grade than the parents, the stats will be proportionally reduced to match the grade's total stat range. For example, Grade 6 parents (50-59 total) producing a Grade 4 foal (30-39 total) will have reduced stats.

---

## Version History

- **v1.0** - Initial breeding system with basic genetics
- **v1.1** - Added combo system for mares
- **v1.2** - Implemented breeding bonuses (Type 0 & 1)
- **v1.3** - Added appearance genetics (body measurements)
- **v1.4** - Switched to multiplicative inheritance rate system
- **v1.5** - Changed bonuses to boost only stallion's coat
- **v1.6** - Implemented weighted mutation system for low-stat inheritance (current)
- **Future** - Planned: Lineage system, stallion combo tracking

---

## Credits

Breeding system designed and implemented for Story of Alicia private server.

For bugs, suggestions, or questions, please open an issue on GitHub.

