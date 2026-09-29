---
name: healthify-recipe-vegetarian
description: Make a recipe healthier and vegetarian using smart ingredient swaps and cooking-method changes (air fry or bake instead of fry, whole-grain or alt flours, less oil, less added sugar, lower sodium, more protein, tofu/TVP/legumes in place of meat), then save it as a new copy in the user's Deglaze library. Use this skill whenever the user asks to "healthify", "lighten up", "make healthier", "make lower-sodium/lower-fat/higher-protein", "swap in whole grains", "air fry version", or "make vegetarian" any recipe, whether it lives in their Deglaze library or they paste or link it, even if they don't say "vegetarian" or "Deglaze" explicitly.
---

# Healthify Recipe (Vegetarian)

Turn a recipe into a healthier, vegetarian version that still tastes like the original. The user cooks vegetarian, so meat is always replaced. Beyond that they want less oil, less added sugar, lower sodium, and more protein, but they care more about a dish that still feels like itself than about squeezing out every last calorie. Every swap should earn its place.

## Workflow

### 1. Get context and the recipe

1. Call `get_user_context` first. It returns dietary notes, household size, unit preference (metric or standard), and tags. Use the unit preference in the new recipe and honor any allergies or dislikes in the notes.
2. Find the recipe:
   - **In the library:** `search_recipes` (prefer `libraryRecipes`), then `get_recipe_details` for the full ingredients, instructions, yield and time. If several recipes match, show the top few and ask which one.
   - **Shared by the user** (pasted text or a link): use it as-is. Fetch the link if you can.
3. Note the recipe's `id` if it came from the library. You'll pass it as `inspiredBy` when saving.

### 2. Analyze before swapping

Read the whole recipe and list the levers, roughly in order of impact for this user:

- **Meat, poultry, fish, gelatin, meat stock, fish sauce, Worcestershire (contains anchovy), lard, Parmesan (animal rennet)**: replace or flag. Vegetarian means no meat, poultry or fish; treat rennet cheese and stock as "flag it, offer the swap" rather than silently changing them.
- **Frying**: can it move to an air fryer or oven?
- **Oil and butter**: how much is really needed?
- **Sugar**: is it structural (baking) or just flavor (sauces)?
- **Sodium**: salt, soy sauce, canned goods, broth, cheese, cured or packaged items.
- **Refined flour or grains**: can any portion move to whole grain or alt flour?
- **Protein**: is there a natural place to add some?

Then decide which changes are **safe** and which are **significant**.

### 3. Safe vs. significant changes

Keep close to the original. That means:

- **Apply directly** (small, low-risk, and easy to explain): trimming oil, using an air fryer or oven instead of frying, cutting sugar modestly, low-sodium broth or soy sauce, partial whole-grain flour swaps, swapping a meat for a close vegetarian match in a dish where the meat is a supporting player.
- **Ask first** (the dish would change character): replacing the main protein of a dish built around meat (e.g., a bolognese or a burger), full flour replacement, changing the cooking method of a signature technique, anything that shifts texture noticeably. Offer 2-3 concrete options with a one-line tradeoff each, e.g. "Crumbled TVP gives the most meaty chew; extra-firm tofu crumbled and browned is milder and higher in moisture; lentils are earthier and softer." Wait for the user's pick before building the final version.

The reason for asking rather than guessing: a healthier recipe nobody wants to eat isn't healthier. When the user has a choice, they'll actually cook it.

If the user asked for one specific change ("air fry version of this"), do that change well and mention other opportunities in one line instead of applying them.

### 4. Choose substitutions

Consult `references/substitutions.md` for ratios, cooking adjustments and pitfalls. Highlights:

- **Meat**: crumbled extra-firm tofu, TVP (rehydrated in vegetable broth), tempeh, lentils, mushrooms, jackfruit, seitan (note: it's not gluten-free), chickpeas.
- **Flours**: buckwheat, whole wheat, oat, almond. These don't behave like all-purpose flour. Partial swaps are the default; see the reference file for percentages.
- **Cooking method**: air fry or bake instead of deep or pan fry.
- **Sugar**: fine to keep, but reduce where the recipe tolerates it or use fruit, maple, or honey sparingly. Don't reach for artificial sweeteners. If one seems truly useful, offer it as an option and keep it modest.
- **Fat and dairy**: they're fine to keep. Offer lower-fat options (Greek yogurt, part-skim cheese, reduced-fat versions) as choices, not mandates.
- **Sodium**: low-sodium broth and soy sauce, rinse canned beans, season with acid, herbs, spices and aromatics to compensate.

### 5. Adjust the method

Substitutions change more than the ingredient list. Update quantities, temperatures, times, and steps to match: air fryer temperatures are typically lower and times shorter than oven-frying; TVP needs rehydrating; whole-grain doughs need more liquid and rest time. Rewrite any step that references removed ingredients. Don't leave the instructions describing browning ground beef in a vegetarian recipe.

### 6. Present the result

Show the user, in this order:

1. **Swaps table**: original ingredient, replacement, and why (goal it serves: less oil, more protein, etc.).
2. **Adjusted quantities, temperatures and times**, called out where they differ from the original.
3. **Texture and flavor notes**: one short line per meaningful swap saying what will feel or taste different.
4. **Nutrition estimate (optional)**: include only when you can give an honest ballpark, e.g. "roughly 30% less oil, ~10 g more protein per serving". Say it's an estimate. If the recipe lacks the data for a credible estimate, skip it rather than inventing numbers.
5. The full new ingredient list and instructions, so they can review before saving.

Keep it scannable. A table plus short lines beats paragraphs.

### 7. Save as a new copy

Save with `add_recipe`. Never `edit_recipe`. The original stays untouched because the user may want to cook it as written.

- `title`: `Healthier: <original title>`
- `inspiredBy`: the original recipe's ID (when it came from the library)
- `ingredients` and `instructions`: keep the original's section structure where it exists (`sectionName`), in the user's preferred units
- `recipeYield`, `totalTimeMinutes`: carry over, updating time if the method changed it
- `notes`: a compact summary of what changed and why, plus any texture or flavor cautions, so the recipe is self-explanatory when they open it months later
- `description`: one short line

Save after the user has seen the result and hasn't objected. If they asked for the healthified recipe and there were no "ask first" changes, saving right after presenting is fine, but say plainly that it has been saved and under what title. If they want tweaks afterward, save a revised copy or ask whether to replace the copy you just made. Don't touch the original.

## Things to get right

- **Vegetarian is a hard constraint.** Double-check hidden animal products (stock, fish sauce, anchovy, gelatin, lard, rennet). If one is buried in the recipe, swap it or flag it.
- **Ask only when it matters.** Household size and equipment (does the user have an air fryer?) are worth asking when they change the recipe. If the recipe calls for air frying and you don't know whether they have one, say so and give the oven alternative in the same answer instead of blocking on the question.
- **Be honest about tradeoffs.** If a swap won't work well (almond flour in yeast bread, for instance), say that and propose something else instead of forcing it.
- **Don't reformat what doesn't need changing.** Preserve the original's voice and steps wherever the recipe isn't affected.
