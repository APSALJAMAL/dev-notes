# auto complete

### mark as complete
```js
(function() {
    const accordions = document.querySelectorAll(
        '.accordion-panel-module--panel-toggler--WUiNu'
    );
    let index = 0;

    function processAccordion() {
        if (index >= accordions.length) {
            console.log("🎉 All accordions processed!");
            return;
        }

        const acc = accordions[index];

        // Expand accordion if not already
        if (acc.getAttribute('aria-expanded') === "false") {
            acc.click();
        }

        console.log(`📂 Opened accordion ${index + 1}/${accordions.length}`);

        // Wait a little so Udemy loads items
        setTimeout(() => {
            // Mark all checkboxes in the whole document (not just this section)
            const checkboxes = document.querySelectorAll(
                'input[type="checkbox"][data-purpose="progress-toggle-button"]'
            );

            let marked = 0;
            checkboxes.forEach(cb => {
                if (!cb.checked) {
                    cb.click();
                    marked++;
                }
            });

            console.log(`✅ Marked ${marked} items in accordion ${index + 1}`);

            index++;
            // Move to next accordion
            setTimeout(processAccordion, 1200);
        }, 1500);
    }

    processAccordion();
})();

```
### auto play
```js
(function () {
    function completeVideo() {
        const video = document.querySelector('video');

        if (video) {
            video.currentTime = video.duration - 2; // jump near end
            console.log("⏭ Skipped to end of video");
        } else {
            console.log("⚠️ No video element found");
        }
    }

    // Run every few seconds (in case video loads late)
    setInterval(completeVideo, 5000);
})();

```