
Create a patch item from a pick

At it's most basic it would just be:
• find line position via top and/or bottom anchor. We want at least 2 lines
• convert pick to change. Walk the text applying the change.

There are some strategies to fix common anchor misses:
• If an anchor is missing, the nearby change can be used instead. Use either
  the removed or added lines. For instance, if we are supposed to remove lines
  then try and find them. Conversely if the patch was already applied then the
  supposed-to-be added lines will already be there!
• When adding, existing identical text is okay.
• When removing, missing text is okay as long as it's followed by an anchor of
  some kind
• Keep lines act as an anchor (for above) but are otherwise not required - they
  are "free" to change or be removed. If they are missing then the algorithm will
  try to continue the change with or without them (dynamic programming)
* empty lines are entirely ignored and are not considered an anchor
