---
layout: post
title:  "Copying code the right way"
---

Sometimes the most pragmatic way to solve a problem is by copying code--forking a small subset of some other repo into your codebase.
Doing some Git&nbsp;Gymnastics™ can help your future self when you inevitably need to take updates of the code you're copying.

* TOC
{:toc}

## When to copy

When you can't avoid it.

## Features of a well-done copy

What do you, the future maintainer of some code, want to see when you encounter some code that was copied from another repo?

* You want to be able to **make local changes**, which is probably why you forked the code in the first place.
* You want to easily **attribute it to its source**, so you can see what's going on upstream. Maybe in the next release they solved your problem, so you can reference via package.
* You want to easily **update the local copy** when a relevant change happens upstream.
* You want the code to **play nicely in your repo**, fitting into your build system and not causing too many warnings or errors.

Some of these are in tension: if upstream uses tabs instead of spaces, or `snake_case` instead of `camelCase`, you probably _don't_ want to change every line; that would make updating hard. Likewise, if you're going to rewrite the whole thing, just do that, don't fork first.

## How to do it (summary)

```bash
git checkout --orphan code-from-WHEREVER
cp /the/origin/repo/path/to/file destination/in/your/repo
git add destination/in/your/repo
git commit -m "Code import from URL at $(pushd /the/origin/repo/ && git rev-parse HEAD)"
git checkout your-branch
git merge --no-commit --allow-unrelated-histories code-from-WHEREVER
# integrate the code into your repo
git add . # stage all the changes as well as the merge
git commit
# optional: apply private changes
```

To update,

```bash
git checkout code-from-WHEREVER # or SHA of the orphan commit from before
                                # if you didn't push the branch
cp /the/origin/repo/path/to/file destination/in/your/repo
git add destination/in/your/repo
git commit -m "Code import from URL at $(pushd /the/origin/repo/ && git rev-parse HEAD)"
git checkout your-update-branch
git merge code-from-WHEREVER
# resolve merge conflicts
# build and test and fix any new bugs
```

## Detailed walkthough



<meta charset="UTF-8">
<style type="text/css">
        @font-face {
            font-family: "Atlassian Icons";
            src: url(http://aui-cdn.atlassian.com/aui-adg/5.9.6/css/fonts/atlassian-icons.eot);
            src: url(http://aui-cdn.atlassian.com/aui-adg/5.9.6/css/fonts/atlassian-icons.eot?#iefix) format("embedded-opentype"), url(http://aui-cdn.atlassian.com/aui-adg/5.9.6/css/fonts/atlassian-icons.woff) format("woff"), url(http://aui-cdn.atlassian.com/aui-adg/5.9.6/css/fonts/atlassian-icons.ttf) format("truetype"), url(http://aui-cdn.atlassian.com/aui-adg/5.9.6/css/fonts/atlassian-icons.svg#atlassian-icons) format("svg");
            font-weight: normal;
            font-style: normal;
        }
        #bit-booster-tbl, #bit-booster-tbl * { line-height: 1.0; font-family: monospace; border-spacing: 0; margin:0; border: 0; padding: 0; font-size: 16px;}
        #bit-booster-tbl span.icon { font-family: "Atlassian Icons"; font-size: 16px; padding-left: 2px; }
        #bit-booster-tbl td.d { font-style: italic; color: darkgray; padding-left: 0.7em; white-space: nowrap; }
        #bit-booster-tbl td.commit { padding: 4px 1px; }
</style>
<!-- http://bit-booster.com/graph.html  DATA WAS:
04b9584|3b7ae0b ef18410| (feature-branch-importing-code)
ef18410|| (import-from-magic)
3b7ae0b||
-->
<table id="bit-booster-tbl" style="width: 1%;">
    <tbody>
        <tr>
            <td rowspan="99999" style="vertical-align: top;">
                <svg
                    xmlns:xlink="http://www.w3.org/1999/xlink" width="39" height="69" text-rendering="optimizeLegibility" style="border: 0px; margin: 0px; padding: 0;" id="bit-booster">
                    <path d="M9,11C8,37.25,25,19.75,24,35" stroke-width="2" stroke-opacity="1" opacity="1" fill="none" stroke="#79c753"></path>
                    <path d="M9,11L9,59" stroke-width="2" stroke-opacity="1" opacity="1" stroke="#034f84"></path>
                    <path d="M24,35L24,35" stroke-width="2" stroke-opacity="1" opacity="1" stroke="#79c753"></path>
                    <circle id="C_3b7ae0b" cx="9" cy="59" r="4" fill="#034f84" stroke="none"></circle>
                    <circle id="C_ef18410" cx="24" cy="35" r="4" fill="#79c753" stroke="none"></circle>
                    <circle id="C_04b9584" cx="9" cy="11" r="4" fill="#034f84" stroke="none"></circle>
                </svg>
            </td>
            <td style="width: 99%; vertical-align: top;"></td>
        </tr>
        <tr id="T_04b9584" data-commitid="04b9584">
            <td class="commit">04b9584</td>
            <td class="d"> feature-branch-importing-code
                <span class="icon"></span>
            </td>
            <td></td>
            <td></td>
        </tr>
        <tr id="T_ef18410" data-commitid="ef18410">
            <td class="commit">ef18410</td>
            <td class="d"> import-from-magic
                <span class="icon"></span>
            </td>
            <td></td>
            <td></td>
        </tr>
        <tr id="T_3b7ae0b" data-commitid="3b7ae0b">
            <td class="commit">3b7ae0b</td>
            <td></td>
            <td></td>
            <td></td>
        </tr>
    </tbody>
</table>

<meta charset="UTF-8">
<style type="text/css">
        @font-face {
            font-family: "Atlassian Icons";
            src: url(http://aui-cdn.atlassian.com/aui-adg/5.9.6/css/fonts/atlassian-icons.eot);
            src: url(http://aui-cdn.atlassian.com/aui-adg/5.9.6/css/fonts/atlassian-icons.eot?#iefix) format("embedded-opentype"), url(http://aui-cdn.atlassian.com/aui-adg/5.9.6/css/fonts/atlassian-icons.woff) format("woff"), url(http://aui-cdn.atlassian.com/aui-adg/5.9.6/css/fonts/atlassian-icons.ttf) format("truetype"), url(http://aui-cdn.atlassian.com/aui-adg/5.9.6/css/fonts/atlassian-icons.svg#atlassian-icons) format("svg");
            font-weight: normal;
            font-style: normal;
        }
        #bit-booster-tbl, #bit-booster-tbl * { line-height: 1.0; font-family: monospace; border-spacing: 0; margin:0; border: 0; padding: 0; font-size: 16px;}
        #bit-booster-tbl span.icon { font-family: "Atlassian Icons"; font-size: 16px; padding-left: 2px; }
        #bit-booster-tbl td.d { font-style: italic; color: darkgray; padding-left: 0.7em; white-space: nowrap; }
        #bit-booster-tbl td.commit { padding: 4px 1px; }
</style>
<!-- http://bit-booster.com/graph.html  DATA WAS:
d01fc24|3b7ae0b 04b9584| (HEAD -> master)
04b9584|3b7ae0b ef18410| (feature-branch-importing-code)
ef18410|| (import-from-magic)
3b7ae0b||
-->
<table id="bit-booster-tbl" style="width: 1%;">
    <tbody>
        <tr>
            <td rowspan="99999" style="vertical-align: top;">
                <svg
                    xmlns:xlink="http://www.w3.org/1999/xlink" width="54" height="93" text-rendering="optimizeLegibility" style="border: 0px; margin: 0px; padding: 0;" id="bit-booster">
                    <path d="M9,11C8,37.25,25,19.75,24,35" stroke-width="2" stroke-opacity="1" opacity="1" fill="none" stroke="#79c753"></path>
                    <path d="M39,59C40,85.25,8,67.75,9,83" stroke-width="2" stroke-opacity="1" opacity="1" fill="none" stroke="#f7786b"></path>
                    <path d="M24,35C23,61.25,40,43.75,39,59" stroke-width="2" stroke-opacity="1" opacity="1" fill="none" stroke="#f7786b"></path>
                    <path d="M39,59L39,59" stroke-width="2" stroke-opacity="1" opacity="1" stroke="#f7786b"></path>
                    <path d="M24,35L24,59" stroke-width="2" stroke-opacity="1" opacity="1" stroke="#79c753"></path>
                    <path d="M9,11L9,83" stroke-width="2" stroke-opacity="1" opacity="1" stroke="#034f84"></path>
                    <path d="M24,35L24,35" stroke-width="2" stroke-opacity="1" opacity="1" stroke="#79c753"></path>
                    <circle id="C_3b7ae0b" cx="9" cy="83" r="4" fill="#034f84" stroke="none"></circle>
                    <circle id="C_ef18410" cx="24" cy="59" r="4" fill="#79c753" stroke="none"></circle>
                    <circle id="C_04b9584" cx="24" cy="35" r="4" fill="#79c753" stroke="none"></circle>
                    <circle id="C_d01fc24" cx="9" cy="11" r="4" fill="#034f84" stroke="none"></circle>
                </svg>
            </td>
            <td style="width: 99%; vertical-align: top;"></td>
        </tr>
        <tr id="T_d01fc24" data-commitid="d01fc24">
            <td class="commit">d01fc24</td>
            <td class="d"> HEAD -&gt; master
                <span class="icon"></span>
            </td>
            <td></td>
            <td></td>
        </tr>
        <tr id="T_04b9584" data-commitid="04b9584">
            <td class="commit">04b9584</td>
            <td class="d"> feature-branch-importing-code
                <span class="icon"></span>
            </td>
            <td></td>
            <td></td>
        </tr>
        <tr id="T_ef18410" data-commitid="ef18410">
            <td class="commit">ef18410</td>
            <td class="d"> import-from-magic
                <span class="icon"></span>
            </td>
            <td></td>
            <td></td>
        </tr>
        <tr id="T_3b7ae0b" data-commitid="3b7ae0b">
            <td class="commit">3b7ae0b</td>
            <td></td>
            <td></td>
            <td></td>
        </tr>
    </tbody>
</table>

### Updating the copied code

<meta charset="UTF-8">
<style type="text/css">
        @font-face {
            font-family: "Atlassian Icons";
            src: url(http://aui-cdn.atlassian.com/aui-adg/5.9.6/css/fonts/atlassian-icons.eot);
            src: url(http://aui-cdn.atlassian.com/aui-adg/5.9.6/css/fonts/atlassian-icons.eot?#iefix) format("embedded-opentype"), url(http://aui-cdn.atlassian.com/aui-adg/5.9.6/css/fonts/atlassian-icons.woff) format("woff"), url(http://aui-cdn.atlassian.com/aui-adg/5.9.6/css/fonts/atlassian-icons.ttf) format("truetype"), url(http://aui-cdn.atlassian.com/aui-adg/5.9.6/css/fonts/atlassian-icons.svg#atlassian-icons) format("svg");
            font-weight: normal;
            font-style: normal;
        }
        #bit-booster-tbl, #bit-booster-tbl * { line-height: 1.0; font-family: monospace; border-spacing: 0; margin:0; border: 0; padding: 0; font-size: 16px;}
        #bit-booster-tbl span.icon { font-family: "Atlassian Icons"; font-size: 16px; padding-left: 2px; }
        #bit-booster-tbl td.d { font-style: italic; color: darkgray; padding-left: 0.7em; white-space: nowrap; }
        #bit-booster-tbl td.commit { padding: 4px 1px; }
</style>
<!-- http://bit-booster.com/graph.html  DATA WAS:
e774365|d01fc24 ddd564c| (HEAD -> master)
ddd564c|d01fc24 2e478a8| (update-magic-copy)
2e478a8|ef18410| (import-from-magic)
d01fc24|3b7ae0b 04b9584|
04b9584|3b7ae0b ef18410| (feature-branch-importing-code)
ef18410||
3b7ae0b||
-->
<table id="bit-booster-tbl" style="width: 1%;">
    <tbody>
        <tr>
            <td rowspan="99999" style="vertical-align: top;">
                <svg
                    xmlns:xlink="http://www.w3.org/1999/xlink" width="54" height="165" text-rendering="optimizeLegibility" style="border: 0px; margin: 0px; padding: 0;" id="bit-booster">
                    <path d="M9,11C8,37.25,25,19.75,24,35" stroke-width="2" stroke-opacity="1" opacity="1" fill="none" stroke="#79c753"></path>
                    <path d="M24,35C23,61.25,40,43.75,39,59" stroke-width="2" stroke-opacity="1" opacity="1" fill="none" stroke="#f7786b"></path>
                    <path d="M24,59C25,85.25,8,67.75,9,83" stroke-width="2" stroke-opacity="1" opacity="1" fill="none" stroke="#79c753"></path>
                    <path d="M39,107C40,133.25,23,115.75,24,131" stroke-width="2" stroke-opacity="1" opacity="1" fill="none" stroke="#f7786b"></path>
                    <path d="M9,83C8,109.25,25,91.75,24,107" stroke-width="2" stroke-opacity="1" opacity="1" fill="none" stroke="#79c753"></path>
                    <path d="M39,131C40,157.25,8,139.75,9,155" stroke-width="2" stroke-opacity="1" opacity="1" fill="none" stroke="#f7786b"></path>
                    <path d="M24,107C23,133.25,40,115.75,39,131" stroke-width="2" stroke-opacity="1" opacity="1" fill="none" stroke="#f7786b"></path>
                    <path d="M39,131L39,131" stroke-width="2" stroke-opacity="1" opacity="1" stroke="#f7786b"></path>
                    <path d="M24,107L24,131" stroke-width="2" stroke-opacity="1" opacity="1" stroke="#79c753"></path>
                    <path d="M9,83L9,155" stroke-width="2" stroke-opacity="1" opacity="1" stroke="#034f84"></path>
                    <path d="M24,107L24,107" stroke-width="2" stroke-opacity="1" opacity="1" stroke="#79c753"></path>
                    <path d="M39,59L39,107" stroke-width="2" stroke-opacity="1" opacity="1" stroke="#f7786b"></path>
                    <path d="M24,35L24,59" stroke-width="2" stroke-opacity="1" opacity="1" stroke="#79c753"></path>
                    <path d="M39,59L39,59" stroke-width="2" stroke-opacity="1" opacity="1" stroke="#f7786b"></path>
                    <path d="M9,11L9,83" stroke-width="2" stroke-opacity="1" opacity="1" stroke="#034f84"></path>
                    <path d="M24,35L24,35" stroke-width="2" stroke-opacity="1" opacity="1" stroke="#79c753"></path>
                    <circle id="C_3b7ae0b" cx="9" cy="155" r="4" fill="#034f84" stroke="none"></circle>
                    <circle id="C_ef18410" cx="24" cy="131" r="4" fill="#f7786b" stroke="none"></circle>
                    <circle id="C_04b9584" cx="24" cy="107" r="4" fill="#79c753" stroke="none"></circle>
                    <circle id="C_d01fc24" cx="9" cy="83" r="4" fill="#034f84" stroke="none"></circle>
                    <circle id="C_2e478a8" cx="39" cy="59" r="4" fill="#f7786b" stroke="none"></circle>
                    <circle id="C_ddd564c" cx="24" cy="35" r="4" fill="#79c753" stroke="none"></circle>
                    <circle id="C_e774365" cx="9" cy="11" r="4" fill="#034f84" stroke="none"></circle>
                </svg>
            </td>
            <td style="width: 99%; vertical-align: top;"></td>
        </tr>
        <tr id="T_e774365" data-commitid="e774365">
            <td class="commit">e774365</td>
            <td class="d"> HEAD -&gt; master
                <span class="icon"></span>
            </td>
            <td></td>
            <td></td>
        </tr>
        <tr id="T_ddd564c" data-commitid="ddd564c">
            <td class="commit">ddd564c</td>
            <td class="d"> update-magic-copy
                <span class="icon"></span>
            </td>
            <td></td>
            <td></td>
        </tr>
        <tr id="T_2e478a8" data-commitid="2e478a8">
            <td class="commit">2e478a8</td>
            <td class="d"> import-from-magic
                <span class="icon"></span>
            </td>
            <td></td>
            <td></td>
        </tr>
        <tr id="T_d01fc24" data-commitid="d01fc24">
            <td class="commit">d01fc24</td>
            <td></td>
            <td></td>
            <td></td>
        </tr>
        <tr id="T_04b9584" data-commitid="04b9584">
            <td class="commit">04b9584</td>
            <td class="d"> feature-branch-importing-code
                <span class="icon"></span>
            </td>
            <td></td>
            <td></td>
        </tr>
        <tr id="T_ef18410" data-commitid="ef18410">
            <td class="commit">ef18410</td>
            <td></td>
            <td></td>
            <td></td>
        </tr>
        <tr id="T_3b7ae0b" data-commitid="3b7ae0b">
            <td class="commit">3b7ae0b</td>
            <td></td>
            <td></td>
            <td></td>
        </tr>
    </tbody>
</table>

## Recovering from past mistakes

If you have some copied code in your repo that _wasn't_ done this way, no problem! You can do this retroactively, including getting all of the let-get-do-merges-for-you benefits from the update. It only costs one extra commit.

First, figure out the origin of the copied code. Hopefully it's in the commit message or a comment somewhere.

After finding the source, do the 
