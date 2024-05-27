---
cssclasses:
  - toc-table
tags:
  - "#TOC"
---

```dataviewjs
// Get the current folder dynamically
const currentFolder = dv.current().file.folder;

// Query pages in the current folder and its subfolders, excluding those tagged with #TOC
const pages = dv.pages(`"${currentFolder}" and -#TOC`)
    // Filter out pages that are missing the required properties
    .filter(p => p.file && p.file.cday && p.Type)
    // Sort pages by their creation date in ascending order
    .sort(p => p.file.cday, 'asc')
    // Group pages by their Type property
    .groupBy(p => p.Type);

// Create the table with specified columns
dv.table(["Type", "File", "Created", "Tags", "Hidden Identifier"], 
    // Flatten the grouped pages into rows for the table
    pages.flatMap((group, index) => {
        let typeClass = index % 2; // Alternate between 0 and 1 for row coloring
        let rows = group.rows.map((row, rowIndex) => [
            rowIndex === 0 ? group.key : "", // Show the group type only on the first row of each group
            row.file.link, // Display the file link
            dv.date(row.file.cday), // Display the creation date
            Array.isArray(row.tags) ? row.tags.map(tag => `#${tag}`).join(" ") : "", // Display tags with "#" prefix if tags is an array
            `type-${typeClass}` // Hidden identifier for CSS styling
        ]);
        return rows;
    })
);

// Assign CSS classes based on the hidden identifier with a slight delay to ensure the table is rendered
setTimeout(() => {
    document.querySelectorAll('.toc-table table.dataview tbody tr').forEach(row => {
        const identifierCell = row.cells[4]; // Assuming the 5th column is the Hidden Identifier
        if (identifierCell) {
            const identifier = identifierCell.innerText.trim(); // Get the identifier text and trim any extra spaces
            if (identifier) {
                row.classList.add(identifier); // Add the identifier as a class to the row
            }
        }
    });
}, 25); // 25ms delay to ensure the table is rendered

```
