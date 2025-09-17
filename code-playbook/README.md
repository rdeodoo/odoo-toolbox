# Code Style Guide

![Designed for: Odoo PS Tech](https://img.shields.io/badge/Designed%20for-Odoo%20PS%20Tech-017E84)

_This document is following the official [Coding Guidelines] which you
should read first._

It contains a list of the most **common mistakes**, implicit **conventions** and
non-written **best practices** related to Odoo coding.\
Those are important, because they make the code easier to read, easier to debug,
faster to review, reduce the risk of bugs and just help ensuring consistency
between Odoo repositories.

**Basic example:**
If you want to know where is the "Cup" record field of the "Kitchen" model, you
should **always** find it without having to search:
- Exactly in the `/models/kitchen.py` file
- Named exactly `cup_id`

The views (form, list, ..) should be defined exactly in
`/views/kitchen_views.xml`. Not in `/views/kitchen_templates.xml`, this is for
portal/website QWeb views.

👉 **Strict code convention is not a way to be annoying, it's a way to make
everyone life easier and save time to everyone.**

Remember, once you code is done:
- Someone will have to read and review it
- Someone will (most likely) have to debug and fix it later, multiple times
- Someone will have to read and upgrade it, multiple times


## Table of Contents

* [Technical Details](#technical-details)
   * [Use `filtered()`](#use-filtered)
   * [`api.depends` decorator](#apidepends-decorator)
   * [Xpath Tricks](#xpath-tricks)
* [Style](#style)
   * [Alphabetical Ordering](#alphabetical-ordering)
   * [Empty recordset loop](#empty-recordset-loop)
* [Linting](#linting)
   * [Python](#python)
   * [Javascript](#javascript)
* [Commit Message](#commit-message)
* [Misc](#misc)



## Technical Details

### Use `filtered()`




### `api.depends` decorator

A method decorated with `api.depends` won't be executed anymore if the values of
the fields listed in `api.depend()` didn't change.
It means that if your decorator is missing one field, your method won't properly
by invalidated and will keep using a "wrong" value.

```py
name = fields.Char(compute="_compute_name")

@api.depends('color', 'sleeve_type')
def _compute_name(self):
    for rec in self:
        rec.name = f"[{rec.size}] {rec.color} with {rec.sleeve_type} sleeves shirt"
```
In this case, `name` should be changed whenever `size` is changed, but `size`
has been forgotten in the decorator. It means that even if `size` is changed,
the `name` will still display the previous `size` used the last time this method
was called.\
The value will be "wrong" until either `color` or `sleeve_type` is changed.

> [!WARNING]
> Please don't take shortcut like not listing `field_A` because `field_B` is
> already listed and `field_B` should already trigger a change of value in
> `field_A` according to the business code.\
> Also don't omit a field because you think its value should never change.
>
> Both those cases will create bugs at some point, because code can and will
> change. So please always list all the fields, explicitly.

### Trailing commas

Always add trailing commas on multiline elements:
- Method calls
- Object/Dict
- Array/List
- Fields properties list
- Tuples
- ..

This is done for 2 reasons:
1. It will avoid useless diff "noise" when someone will add something to those
   elements after you, as it would require to add the trailing comma on your
   line, including it in the diff.
2. It often causes frustrating issues that will make you lose time debugging
   when you will add a new element but forget to add the missing trailing comma.
   In Python, it won't crash in some cases and will turn the two lines into a
   tuple:
   ```py
   'web.assets_backend': [
       '/some/file',
       '/some/other/very/long/path/to/a/specific/file/very/long/path/like/really/long'
   ]
   ```
   Now, if someone quickly adds his file:
   ```py
   'web.assets_backend': [
       '/some/file',
       '/some/other/very/long/path/to/a/specific/file/very/long/path/like/really/long'
       '/last/file'
   ]
   ```
   One can easily not see the missing comma and just add his element without
   paying too much attention.
   The code won't crash, the array will contains 2 entries with the 2 last lines
   being considered as one multiline string, thus being concatenate into one:
   ```py
   [
       '/some/file',
       '/some/other/very/long/path/to/a/specific/file/very/long/path/like/really/long/last/file',
   ]
   ```

### Xpath Tricks

#### `$0` magic variable

When you replace an element, you can actually output that replaced element using
`$0`, which is generally saving you from a lot of copy paste!

```xml
<xpath expr="//div[hasclass('card-body')]/span" position="replace">
    <a t-attf-href="/customers/#{slug(reference)}">$0</a>
</xpath>
```

With this, we don't need to copy paste what was inside the `span` in the
original template.\
This can of course be used on a whole DOM section, if for instance you want to
wrap an entire part of a view inside a new node.

#### `Move` Xpath

Xpath `position` attribute comes with a handy `move` value.









## Style

### Alphabetical Ordering

Respect alphabetical orders wherever it can be applied:
- in `__init__.py` imports
- in python import `from xx import yy, zz`
- in manifest keys (`data`, `assets`, ..)
- ..

It just helps saving time and avoid overlooking stuff when searching for
something.

### Empty recordset loop
Don't do
```py
if recs:
    for rec in recs:
```
Do
```py
for rec in recs:
```
It won't loop if there are no records. "Protecting" everything in the code when
it's not required adds a lot of noise.

### Favor `Commands`

Use `Commands` instead of the number notation.\n
Number notation is deprecated and will be removed at some point. `Commands` is
straightforward and super explicit. using number notation is like purposely
trying to obfuscate your code.

### Comment your patches

When your last resort is to copy paste a whole method to add some diff inside
it, you **have to**:
- Add a comment a the very top of the method to explain this is a copy paste
  with patches inside it
- Mark each start of a patch with a comment
- Mark each end of a patch with a comment (or the "start" comment is useless)

Do exactly:
```py
def a_method(self):
    # This method is copy pasted and contains a few PATCHES
    some_extisting_code()
    # PATCH START: Change domain
    domain = [('modified', '=', True)]
    # PATCH END
    some_existing_code_using_domain()
    some_other_code()
    # PATCH START: Write the new field on the found records
    found_records.write({'new_field': True})
    # PATCH END
    some_finale_code()
```

<details>

<summary>See a real JS example for illustration</summary>

In this example, you'll see that the method is correctly commented as being a
copy pasted method with patches (using convenient JSDoc notation).
There is a single patch, surrounded by START/END comments.

You can directly see what is customized here in a blink. Given the method size,
you'd have to spend a lot of time or use a text comparison tool to find the
difference otherwise.

```js
async updatePrograms() {
    /**
     * Copy pasted code, see `PATCH`
     *
     * @override
     */
    const order = this.get_order();
    // 'order.delivery_provider_id' check is used for UrbanPiper orders (as loyalty points and rewards are not allowed for UrbanPiper orders)
    if (!order || order.delivery_provider_id) {
        return;
    }
    const changesPerProgram = {};
    const programsToCheck = new Set();
    // By default include all programs that are considered 'applicable'
    for (const program of this.models["loyalty.program"].getAll()) {
        if (order._programIsApplicable(program)) {
            programsToCheck.add(program.id);
        }
    }
    for (const pe of Object.values(order.uiState.couponPointChanges)) {
        if (!changesPerProgram[pe.program_id]) {
            changesPerProgram[pe.program_id] = [];
            programsToCheck.add(pe.program_id);
        }
        changesPerProgram[pe.program_id].push(pe);
    }
    for (const coupon of order._code_activated_coupon_ids) {
        programsToCheck.add(coupon.program_id.id);
    }
    const programs = [...programsToCheck].map((programId) =>
        this.models["loyalty.program"].get(programId)
    );
    const pointsAddedPerProgram = order.pointsForPrograms(programs);
    for (const program of this.models["loyalty.program"].getAll()) {
        // Future programs may split their points per unit paid (gift cards for example), consider a non applicable program to give no points
        const pointsAdded = order._programIsApplicable(program)
            ? pointsAddedPerProgram[program.id]
            : [];
        // For programs that apply to both (loyalty) we always add a change of 0 points, if there is none, since it makes it easier to
        //  track for claimable rewards, and makes sure to load the partner's loyalty card.
        if (program.is_nominative && !pointsAdded.length && order.get_partner()) {
            pointsAdded.push({ points: 0 });
        }
        const oldChanges = changesPerProgram[program.id] || [];
        // Update point changes for those that exist
        for (
            let idx = 0;
            idx < Math.min(pointsAdded.length, oldChanges.length) && !oldChanges[idx].manual;
            idx++
        ) {
            Object.assign(oldChanges[idx], pointsAdded[idx]);
        }
        if (pointsAdded.length < oldChanges.length || !order._programIsApplicable(program)) {
            const removedIds = oldChanges.map((pe) => pe.coupon_id);
            order.uiState.couponPointChanges = Object.fromEntries(
                Object.entries(order.uiState.couponPointChanges).filter(([_, pe]) => {
                    return !removedIds.includes(pe.coupon_id);
                })
            );
        } else if (pointsAdded.length > oldChanges.length) {
            const pointsCount = pointsAdded.reduce((acc, pointObj) => {
                const { points, barcode = "" } = pointObj;
                const key = barcode ? `${points}-${barcode}` : `${points}`;
                acc[key] = (acc[key] || 0) + 1;
                return acc;
            }, {});

            oldChanges.forEach((pointObj) => {
                const { points, barcode = "" } = pointObj;
                const key = barcode ? `${points}-${barcode}` : `${points}`;
                if (pointsCount[key] && pointsCount[key] > 0) {
                    pointsCount[key]--;
                }
            });

            // Get new points added which are not in oldChanges
            const newPointsAdded = [];
            Object.keys(pointsCount).forEach((key) => {
                // PATCH: ignore the first -, treat as negative number
                // Remove it because it breaks parsing where expected format is amount-barcode
                let isNegative = false;
                let keyToSplit = key;
                if (key.charAt(0) === '-') {
                    keyToSplit = keyToSplit.substring(1);
                    isNegative = true;
                }
                const [points, barcode = ""] = keyToSplit.split("-");
                while (pointsCount[key] > 0) {
                    let pointsNumber = Number(points);
                    if (isNegative) {
                        pointsNumber *= -1;
                    }
                    newPointsAdded.push({ points: pointsNumber, barcode});
                    pointsCount[key]--;
                }
                // End patch
            });

            for (const pa of newPointsAdded) {
                const coupon = await this.couponForProgram(program);
                const couponPointChange = {
                    points: pa.points,
                    program_id: program.id,
                    coupon_id: coupon.id,
                    barcode: pa.barcode,
                    appliedRules: pointsForProgramsCountedRules[program.id],
                };
                if (program && program.program_type === "gift_card") {
                    couponPointChange.product_id =
                        order.get_selected_orderline()?.product_id.id;
                    couponPointChange.expiration_date = serializeDate(
                        luxon.DateTime.now().plus({ year: 1 })
                    );
                    couponPointChange.code = order.get_selected_orderline()?.gift_code;
                    couponPointChange.partner_id = order.get_partner()?.id;
                }

                order.uiState.couponPointChanges[coupon.id] = couponPointChange;
            }
        }
    }
},
```

</details>

Otherwise, we have to copy paste your method and the standard method in text
comparison tools to figure what you changed.



## Linting

You should have both a JS and PY linter.\
It's not just for the code to be "beautiful", every rule serves a purpose to
prevent mistakes while coding (except arguably E302 🙊), even if you don't
realize it.

### Python
Odoo historically used `Flake8`/`Pep8`, it is still the [official] suggested
one.\
Odoo RD is now using `Ruff` to speed up runbot, as it is way faster, and saving
resources was a priority for runbot.
Use the one you prefer, I personally suggest `Flake8` because `Ruff` is still
a WIP and lacking many rules, see https://github.com/astral-sh/ruff/issues/2402
and https://github.com/astral-sh/ruff/pull/13585

For instance, `Ruff` will consider this perfectly fine 😱:
```py
a = {'1':   '1',  # Notice the multiple spaces after operator here
    '2': '2',
        '3': '3',
'4': '4',
            '5': '5',
}
```

See [How to install flake8 linter](TODO)

### Javascript
You should use `eslint`, ideally `v8.27`.

See [How to install eslint linter](TODO)

> [!WARNING]
> Don't use `eslint` > `v9`, they refactored everything and you won't be able to
> easily install it. Among other things, the way the configuration files work
> has been fully changed.










## Commit Message

### Task ID Reference

Reference the task ID in your commit message, people will blame your code and
will need to quickly identify the related task to the LOC they are currently
working on.\
It should be part of your commit message.

There are two ways to do that, the _official_ one being preferred as the
_upgrade_ one is "wasting" 9 characters on the title automatically, while we are
already struggling to have the commit message fit in 72 characters.
Also, the _official_ one is following more the Git/Github spirit, see [autolink]
and [Git Trailers].

**Official convention** (end of message on a separate line):
```
[TAG] module: title

Description.

task-1234567
```

**Upgrade convention** (inside a tag in title):
```
[TAG][1234567] module: title

Description.
```

Note that for bugfixes (`Help` project), it should be `opw-id`, not `task-id`.
> [!TIP]
> `opw` stands for `OpenErp Publisher Warranty`

### Commit message matter

Write useful information inside the commit message, not only on the PR.
PR messages are not as important, and often not even looked at since it will
often be outdated compared to the commit message (ideally, it shouldn't).

PR message have a very short lifespan, once you merged your commit, no one will
ever read it again.\
Your commit message, on the other hand, will be read forever as it is in the
history of the repository. It is also the only thing people will see when
blaming the code.

### Never use merge commit

There are a few reasons not to use it:
- The history becomes ugly, without any added value since we mostly with little
  branches at a time, we mostly work with linear history anyway.
- History becomes a mess overtime, adding extra nested hierarchy levels even
  for simple branches targeting the staging.
- Merge commit have no sense/meaning in itself most of the time. Some will just
  contains 2 commits (or even 1..) that were perfectly fine as is.
- When blaming commits during debugging, we only care about commit orders, not
  merge "hierarchy" history.
- It diverges from what Odoo RD is doing. Let's keep things as close to RD as
  possible when it makes sense. One company, less rules. Especially since there
  is hundreds of developer working on the same branches in RD and it works
  perfectly fine with flat hierarchy, from development phase to fix/debugging
  phase later.

There are some more reasons, but those ones are related to the wrong usage
people make of merge commit, not merge commit iself:
- People ship temporary/wip commit in the merge commits
  !(Merge Commit Wip Commits)[img/merge-commit-wip-commit.png]
- People don't bother having a clean commit history (even if no wip commit)
  inside the merge commit (so, basically, inside the PR) with clear commit
  message and clear code division
- Doing the merge commit through the GIT UI creates commit with a weird and
  incorrect author email (autogenerated noreply)
  !(Wrong Author Merge Commit)[img/wrong-author-merge-commit.png]
  > [!TIP]
  > You can fully disable merge commit button from PR on the repository settings.

In practice, it is always wrongly used and systematically lead to awful and
impossible to use/understand spaghetti commits history:

!(Spaghetti History Merge Commit)[img/spaghetti-history-merge-commit.png]





## Misc

### Odoo.sh dev branches

Once your PR is merged, directly **delete your dev branch**.
Never keep useless dev branches. You can restore it later should you need it.

It will avoid having SH projects cluttered with stale dev branches.\
We want to keep it clean and usable.

Keeping "dead" dev branches is making the Odoo.sh looks more active than what it
really is, and brings confusion when someone opens the project.

| Before Cleaning | After Cleaning |
| - | - |
| !(SH Before Cleaning)[img/sh-before-cleaning.png] | !(SH After Cleaning)[img/sh-after-cleaning.png] |

> [!TIP]
> When opening an SH project, if it looks messy with old dev branches, ideally
  ping the devs and ask them to clean it up. Takes 2 min, keeps things clear.




[Coding Guidelines]: https://www.odoo.com/documentation/18.0/contributing/development/coding_guidelines.html
[official]: https://www.odoo.com/documentation/18.0/contributing/development/coding_guidelines.html#pep8-options
[autolink]: https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/managing-repository-settings/configuring-autolinks-to-reference-external-resources
[Git Trailers]: https://git-scm.com/docs/git-interpret-trailers
