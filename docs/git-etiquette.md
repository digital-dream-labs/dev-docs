[Table of Contents](index.md)
## Git Etiquette

History matters. If you don't understand what happened before, you are
destined to repeat it.

I personally think good commit messages are more useful than
comments in source code. For one thing, you don't have to maintain
commit messages. They are inherently coupled to a snapshot
of the code. Thus, they should reflect the reasoning for why changes
were made. The way I think about it is that if I'm reading some
code and think to myself:

"WTF, why did we do it this way?"

I should be able to look at the blame lines, read the associated
commit comments, and get the answer. Good commit messages
reduce the number of WTFs / min:

http://www.osnews.com/story/19266/WTFs_m

Here are some basic guidelines to achieve this goal.


### Commit Message Format

The best format for commit messages is this:

```
<Title line (50 chars, 69 max)>
<blank line>
<Description: one or more lines with
              _hard linebreaks_ (72 chars or less)>
```

Any commit message that doesn't look like this will break most
tools for analyzing and searching commit messages. Many of
these rely on text-based tools which show the single title
line and only provide space for 50-80 chars.  If you can't summarize
your changes in 50-80 chars, then need to make more
granular commits.


### Title line

The title line should always be a short summary and include references
to JIRA or other tickets, if possible. JIRA will automatically
link commits to tickets, which is useful for automatically tracking
work done on bugs or features.

Please avoid only mentioning an issue number. For those of us with
short term memory loss, the issue number will be meaningless within
a day or so, causing constant cross-referencing with JIRA in order
to determine why the change was made.

When updating pointers to submodules, please avoid a generic comment
such as 'update bs'. Add some additional words describing
the most significant change.

Use the 'git submodule summary' command to print a list of commits
included in the pointer update, and copy that output into the
update commit to make it clear what changes are included.

#### Examples

```
# Bad
Fixing OD-666

# Good
OD-666 Fix issue where Fuzz swears at children.


# Bad
Update BS

# Good
Update BS pointer for lights fixes

* lib/anki/drive-basestation 2e199ca...58f11dc (1):
  > Fixing suchandsuch issue with lights
```

### Change description

The description should include reasoning for why a change was made.
For bugs, include a description of the root cause, if known.
For features, provide a brief overview of what it is, and how to test
it (is relevant). Depending on the number of changes, it can also
be useful to provide additional context about what the changes entail
beyond what can be summarized in the title.

### Good Examples of full commit messages
```
OD-19823 Fix profile buffer overflowing

The exhibited behavior where a client could not join a host
appeared to occur specifically because the host was sending a
large profile buffer >256 bytes, which overflowed the uint_8
length field of the CLAD representation of that buffer.

This change increases the length of that field to 16 bits, so that
it will not overflow.
```
```
OD-19133 Fix bug in lane change to outer lanes

In a previous pass, the lane change controller would use the "measured
lane change progress" as computed by the horizontal motion track module
to determine its estimate of actual lane change progress.

It was changed to use the current-tick and previous-tick measurement to
introduce a smoothing component. However the cached previous-tick value
was not being cleared correctly, which lead to different behavior in
lane changes if you did it after a previous lane change.

The fix is to make the cached previous-tick measurement a static
variable that is cleared when:

1) controller state is reset
2) a new lane change is commanded

```

### See Also ###

https://github.com/RomuloOliveira/commit-messages-guide

