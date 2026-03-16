# 🚀 TriliumNext Global Todo Reporter

**TriliumNext Global Todo Reporter** is a backend script that scans your entire **TriliumNext note tree** for checkbox tasks and generates a clean, centralized **global todo report** inside a dedicated note.

Since **TriliumNext** does not currently include a built-in global task dashboard, this script provides an automated way to see **all tasks across your knowledge base in one place**.

---

# ✨ Features

### 🔎 Deep Scan

Searches **all text notes** for checkboxes regardless of their location or tree depth.

### 📄 Line-by-Line Reporting

Extracts the **actual task text** so you don't have to open every note individually.

### 🔗 Smart Navigation

Task links open the source note in a **new tab or split pane** (`target="_blank"`).

### 🎨 Visual Status

Tasks display with clear visual indicators:

| Status         | Icon   |
| -------------- | ------ |
| Open Task      | 🟧 `□` |
| Completed Task | ✅      |

### 📂 Note Grouping

Tasks are grouped under their **parent note title** with a visual border for clarity.

---

# 🛠️ Setup Instructions

## 1️⃣ Create the Report Note

1. Create a new **Text Note** anywhere in your tree.
2. Name it:

```
Todo Report
```

3. Right-click the note in the sidebar.
4. Select:

```
Copy Note ID
```

5. Save the ID somewhere (example):

```
mZ3MxTN5z5bN
```

---

## 2️⃣ Create the Script Note

1. Create a new note.
2. Set the **Note Type** to:

```
JS backend
```

3. Name the note:

```
Generate Todo Report
```

4. Paste the script below into the note.

5. Replace this value:

```js
YOUR_REPORT_ID_HERE
```

with the **Note ID** you copied earlier.

---

# 📜 Script

```javascript
/**
 * TriliumNext Global Todo Reporter
 * Optimized for TriliumNext backend execution.
 */

(async () => {
    // 1. SET YOUR REPORT NOTE ID HERE
    const reportNoteId = 'YOUR_REPORT_ID_HERE'; 
    
    // 2. Scan all notes
    const notes = await api.searchForNotes(''); 
    
    let report = "<h2>Comprehensive Todo Report</h2>";
    report += "<p><i>Clicking a link will open the note in a new tab.</i></p><hr>";
    let totalItemsFound = 0;

    for (let note of notes) {
        // Skip the report note itself and non-text notes
        if (note.noteId === reportNoteId || note.type !== 'text') continue;

        const content = await note.getContent(); 
        
        // Match list items that are checkboxes
        const liRegex = /<li[^>]*>(.*?)<\/li>/gi;
        let match;
        let noteTodosHtml = "";
        let noteHasTodos = false;

        while ((match = liRegex.exec(content)) !== null) {
            const liFullHtml = match[0];
            const liInnerHtml = match[1];

            // Detect if the list item is a checkbox
            if (liFullHtml.includes('data-checkbox') || liFullHtml.includes('cbx') || liFullHtml.includes('checkbox')) {
                
                const isChecked = liFullHtml.includes('checked') || liFullHtml.includes('cbx-check');
                
                // Status Styling
                const status = isChecked 
                    ? "✅" 
                    : "<span style='color: orange; font-weight: bold; font-size: 1.1em;'>□</span>";
                
                // Strip HTML tags
                const cleanText = liInnerHtml.replace(/<[^>]*>?/gm, '').trim();
                
                if (cleanText) {
                    noteTodosHtml += `<div style="margin-left: 20px; margin-bottom: 4px;">${status} ${cleanText}</div>`;
                    noteHasTodos = true;
                    totalItemsFound++;
                }
            }
        }

        // Add note grouping
        if (noteHasTodos) {
            report += `<div style="margin-top: 15px; border-left: 3px solid #ffa500; padding-left: 10px;">
                <strong><a href="#root/${note.noteId}" target="_blank">${note.title}</a></strong>
                ${noteTodosHtml}
            </div>`;
        }
    }

    // 3. Update the report note
    const reportNote = api.getNote(reportNoteId);
    if (reportNote) {
        await reportNote.setContent(report);
        api.printHtml(`<h3>Scan Complete</h3><p>Found <b>${totalItemsFound}</b> tasks.</p>`);
    } else {
        api.printHtml("<span style='color:red;'>Error: Report Note ID not found.</span>");
    }
})();
```

---

# ⚙️ Automation & UI Shortcuts

Add the following **labels** to the script note to integrate it smoothly into your workflow.

| Label Name      | Value           | Description                                                |
| --------------- | --------------- | ---------------------------------------------------------- |
| `runOnInterval` | `3600`          | Automatically refresh the report every hour (3600 seconds) |
| `actionButton`  | `Refresh Todos` | Adds a manual **Refresh button** in the note toolbar       |

---

# 📝 Usage Tips

### 🧭 New Tabs

On the desktop version of **TriliumNext**, links using `target="_blank"` typically open the note in a **new split pane**.

### 📄 Legacy Notes

If older notes do not appear in the report, ensure they are set to the **Text** note type.

### 🎨 Custom Icons

You can easily change the task status icons in the script:

```js
"✅"
"□"
```

Replace them with any **emoji or HTML entity** you prefer.

Examples:

```
🔲
🟧
✔️
📝
```

---

# 📊 Example Output

```
Comprehensive Todo Report

Project Planning
□ Write proposal
□ Review design docs
✅ Schedule team meeting

Personal Tasks
□ Buy groceries
✅ Call electrician
```

---

# ❤️ Contributing

Pull requests, improvements, and suggestions are welcome!

If you enhance the script (filters, tagging, priority sorting, etc.), feel free to share it with the community.

---

# 👥 Credits

Created for the **TriliumNext community**.

