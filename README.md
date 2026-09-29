# healthify-recipe-vegetarian

A Claude skill that turns a recipe into a healthier vegetarian version using ingredient swaps and cooking-method changes, then saves it as a new copy in your [Deglaze](https://deglaze.app) library.

## What it does

- Finds a recipe in your Deglaze library, or works from one you paste in
- Replaces meat (tofu, TVP, tempeh, jackfruit, mushrooms, legumes)
- Cuts oil, added sugar and sodium; adds protein
- Swaps in whole-grain and alt flours (whole wheat, buckwheat, oat, almond)
- Air fries or bakes instead of frying
- Stays close to the original, and asks first before making big changes
- Saves a new recipe titled `Healthier: <original title>` and never edits the original

## Install

Copy this folder to your personal skills directory:

```bash
git clone https://github.com/notnolan/healthify-recipe-vegetarian ~/.claude/skills/healthify-recipe-vegetarian
```

Requires the Deglaze connector for library search and saving.

## Use

Ask Claude things like:

- "Healthify my lasagna"
- "Make this recipe healthier"
- "Give me an air fry version of this"
- "Swap in whole grains"

## Files

- `SKILL.md`: the workflow and rules
- `references/substitutions.md`: swap ratios, cooking adjustments and pitfalls
