I just opened my first open-source pull request 🎉

This week I worked on a project: ImprovedTube, a browser extension that adds features to YouTube (4,600+ stars on GitHub).

The bug: you can set a keyboard shortcut to show or hide a video's chapters list. Pressing it once opened the list, but pressing it again never closed it. Every press also logged an error in the browser.

What I found: the bug report said the shortcut failed in full screen. When I reproduced it in a real browser, that's not what happened. So I logged the page's state after every key press and found the real cause. After you open the chapters list, YouTube keeps a hidden copy of it on the page. The extension saw that hidden copy and thought the list was closed, so every press tried to open it again.

The fix: I changed one function so it checks whether a chapters list is actually open before deciding what to do.

The impact:
✅ The second press now closes the list, in normal view and in full screen
✅ Errors per test run went from 4 to 0
✅ A new test with 4 checks: all 4 fail on the old code and pass with the fix
✅ All 233 of the project's tests still pass

What I learned: reproduce the bug before you trust the report, or your own first guess. My first two theories from reading the code were wrong, and running it in the browser settled it in minutes.

The PR is waiting for the maintainers to review it. Thanks to CodePath's AI301 course for walking me through my first real contribution.

🔗 https://github.com/code-charity/youtube/pull/4469

#OpenSource #JavaScript #FirstPR #CodePath #SoftwareEngineering