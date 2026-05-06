# Course skip
```js
function autoFlow() {

    // 🔹 Handle "Incomplete Module" popup
    let laterBtn = [...document.querySelectorAll('button, a, span')]
        .find(el => el.innerText.toLowerCase().includes('complete later'));

    if (laterBtn) {
        console.log("Popup detected → Clicking Complete Later");
        laterBtn.click();
        setTimeout(autoFlow, 1500);
        return;
    }

    // 🔹 Mark as Read if available
    let toggles = document.querySelectorAll('.togBtn');

    if (toggles.length > 0) {
        console.log("Marking content...");
        toggles.forEach((toggle, index) => {
            if (!toggle.checked) {
                setTimeout(() => toggle.click(), index * 150); // faster
            }
        });
    } else {
        console.log("No mark option → Skipping (quiz/assessment)");
    }

    // 🔹 Go to Next
    setTimeout(() => {
        let nextBtn = [...document.querySelectorAll('button, a, span')]
            .find(el => el.innerText.toLowerCase().includes('next'));

        if (nextBtn) {
            console.log("Next page...");
            nextBtn.click();

            // 🔁 Loop again (faster reload wait)
            setTimeout(autoFlow, 3000);
        } else {
            console.log("Finished (No Next button)");
        }
    }, (toggles.length * 200) + 1000);
}

// ▶️ Start
autoFlow();

```

# cut,copy,paste
```
['copy','cut','paste'].forEach(evt => {
  document.addEventListener(evt, e => e.stopImmediatePropagation(), true);
  document['on' + evt] = null;
});
```
