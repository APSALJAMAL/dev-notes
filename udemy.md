# auto complete

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