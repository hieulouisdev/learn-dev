# Lesson 08: Tables

> Module: HTML · Lesson 8 of 13
> Estimated time: 45–60 minutes

Tables let you display data in rows and columns — like a spreadsheet. They are perfect for schedules, comparison charts, financial data, and any information that fits a grid. In this lesson, you will learn how to build tables that are both accessible and easy to style.

---

## Learning Objectives

After this lesson, you will be able to:

1. Build a basic table using `<table>`, `<tr>`, `<th>`, and `<td>`.
2. Add table sections: `<thead>`, `<tbody>`, `<tfoot>`.
3. Merge cells with `colspan` and `rowspan`.
4. Add a caption with `<caption>`.
5. Group columns with `<colgroup>` and `<col>`.
6. Avoid the common mistake of using tables for layout.

---

## 1. The Four Core Tags

Every table is built from the same four tags:

| Tag | Stands For | Purpose |
|-----|-----------|---------|
| `<table>` | Table | Wraps the whole table. |
| `<tr>` | Table Row | One horizontal row of cells. |
| `<th>` | Table Header cell | A bold, centered cell that labels a row or column. |
| `<td>` | Table Data cell | One regular cell of data. |

Here is the smallest useful table:

```html
<table>
  <tr>
    <th>Name</th>
    <th>Age</th>
    <th>City</th>
  </tr>
  <tr>
    <td>Alice</td>
    <td>30</td>
    <td>Paris</td>
  </tr>
  <tr>
    <td>Bob</td>
    <td>25</td>
    <td>Tokyo</td>
  </tr>
</table>
```

Renders as a grid:

```
Name    Age    City
Alice   30     Paris
Bob     25     Tokyo
```

The first row uses `<th>` for the column headers. The data rows use `<td>` for each cell. The browser bolds and centers `<th>` by default to make them stand out.

---

## 2. Table Sections: `<thead>`, `<tbody>`, `<tfoot>`

For tables of any meaningful size, group rows into three sections:

- `<thead>` — the header row(s).
- `<tbody>` — the body rows (where your data lives).
- `<tfoot>` — the footer row(s), typically used for totals or summaries.

```html
<table>
  <thead>
    <tr>
      <th>Product</th>
      <th>Price</th>
      <th>Quantity</th>
      <th>Subtotal</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Coffee</td>
      <td>$3</td>
      <td>2</td>
      <td>$6</td>
    </tr>
    <tr>
      <td>Croissant</td>
      <td>$2.50</td>
      <td>3</td>
      <td>$7.50</td>
    </tr>
  </tbody>
  <tfoot>
    <tr>
      <td colspan="3">Total</td>
      <td>$13.50</td>
    </tr>
  </tfoot>
</table>
```

Why use sections? Three reasons:

1. **Styling**: You can apply different styles to header, body, and footer.
2. **Printing**: When printing, the `<thead>` and `<tfoot>` repeat on every page.
3. **Scrolling**: A `<tbody>` with fixed height and `overflow-y: auto` can scroll independently of the header.

---

## 3. Cell Merging: `colspan` and `rowspan`

You can merge a cell across multiple columns or multiple rows.

### `colspan` — Merge Columns

```html
<td colspan="2">This cell spans two columns</td>
```

In the total row above, we used `colspan="3"` to make "Total" span across three columns.

### `rowspan` — Merge Rows

```html
<td rowspan="2">This cell spans two rows</td>
```

Example: a schedule where one event spans multiple time slots.

```html
<table>
  <tr>
    <th>Time</th>
    <th>Monday</th>
    <th>Tuesday</th>
  </tr>
  <tr>
    <td>9:00</td>
    <td rowspan="2">Math Workshop (9-11)</td>
    <td>English</td>
  </tr>
  <tr>
    <td>10:00</td>
    <!-- Monday cell is occupied by rowspan from above -->
    <td>History</td>
  </tr>
</table>
```

Notice that the second row has only two `<td>`s — the Monday cell is "continued" from the row above. Count carefully: when using `rowspan`, you must omit the cell in the rows below.

---

## 4. The `<caption>` Element

A table caption is a title for the table. It should be the first child of `<table>`:

```html
<table>
  <caption>Monthly Expenses for August 2026</caption>
  <thead>
    <tr>
      <th>Category</th>
      <th>Amount</th>
    </tr>
  </thead>
  <tbody>
    <tr><td>Rent</td><td>$800</td></tr>
    <tr><td>Food</td><td>$300</td></tr>
  </tbody>
</table>
```

By default, the caption appears centered above the table. Use it instead of a separate `<h2>` heading — it's semantically tied to the table.

---

## 5. Column Groups: `<colgroup>` and `<col>`

When you want to style entire columns, use `<colgroup>` and `<col>`:

```html
<table>
  <colgroup>
    <col style="background: #f0f0f0;">
    <col style="background: white;">
    <col style="background: white;">
  </colgroup>
  <tr>
    <th>Name</th>
    <th>Email</th>
    <th>Phone</th>
  </tr>
  <tr>
    <td>Alice</td>
    <td>alice@example.com</td>
    <td>555-0100</td>
  </tr>
</table>
```

The `<col>` elements apply styles to entire columns. Note: only a few CSS properties work on `<col>` (background, border, width, visibility).

You can also span multiple columns:

```html
<colgroup>
  <col span="2" style="background: #f0f0f0;">
  <col style="background: white;">
</colgroup>
```

---

## 6. The `scope` Attribute for Accessibility

To help screen readers understand which header applies to which cells, add `scope` to your `<th>` tags:

```html
<table>
  <tr>
    <th scope="col">Name</th>
    <th scope="col">Age</th>
  </tr>
  <tr>
    <th scope="row">Alice</th>
    <td>30</td>
  </tr>
  <tr>
    <th scope="row">Bob</th>
    <td>25</td>
  </tr>
</table>
```

- `scope="col"` — this header applies to the column below it.
- `scope="row"` — this header applies to the row it's in.

This is essential for accessible tables. Always include `scope` on `<th>` elements.

---

## 7. A Complete Example: A Real Schedule

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Weekly Schedule</title>
  </head>
  <body>
    <h1>My Weekly Schedule</h1>

    <table border="1" cellpadding="8" cellspacing="0">
      <caption>Schedule for the week of August 25–31, 2026</caption>

      <thead>
        <tr>
          <th scope="col">Time</th>
          <th scope="col">Monday</th>
          <th scope="col">Tuesday</th>
          <th scope="col">Wednesday</th>
          <th scope="col">Thursday</th>
          <th scope="col">Friday</th>
        </tr>
      </thead>

      <tbody>
        <tr>
          <th scope="row">9:00</th>
          <td>HTML Study</td>
          <td>HTML Study</td>
          <td>HTML Study</td>
          <td>CSS Study</td>
          <td>CSS Study</td>
        </tr>
        <tr>
          <th scope="row">11:00</th>
          <td>Break</td>
          <td>Break</td>
          <td>Break</td>
          <td>Break</td>
          <td>Break</td>
        </tr>
        <tr>
          <th scope="row">13:00</th>
          <td>Practice</td>
          <td>Practice</td>
          <td>Practice</td>
          <td>Practice</td>
          <td>Practice</td>
        </tr>
        <tr>
          <th scope="row">15:00</th>
          <td colspan="2" rowspan="2">Project Day (Long session)</td>
          <td>Rest</td>
          <td colspan="2" rowspan="2">Project Day (Long session)</td>
        </tr>
        <tr>
          <th scope="row">17:00</th>
          <td>Rest</td>
        </tr>
      </tbody>

      <tfoot>
        <tr>
          <td colspan="6">Total study time: 30 hours per week</td>
        </tr>
      </tfoot>
    </table>
  </body>
</html>
```

Note the `border`, `cellpadding`, and `cellspacing` attributes on `<table>` — these are old HTML attributes for visual styling. In modern web development, you should use CSS instead. We included them here just so the table renders with visible borders without CSS. We will replace them with proper CSS styling in the next module.

---

## 8. The Golden Rule: Don't Use Tables for Layout

In the 1990s and 2000s, developers used tables to lay out entire web pages — putting the navigation in one column, content in another, footer in another row. **This is wrong.**

Tables are for **tabular data** only — information that naturally fits a grid (schedules, prices, statistics, calendars). For page layout, use CSS (flexbox, grid) which we will learn in the CSS module.

### Why Not Tables for Layout?

1. **Accessibility nightmare**: screen readers announce tables as data, which confuses users navigating a layout table.
2. **Not responsive**: tables don't adapt to narrow phone screens — the columns just shrink or scroll horizontally.
3. **Harder to maintain**: changing a table-based layout requires reorganizing rows and columns, while CSS layouts can be reorganized with one property change.

If you ever feel tempted to use a table for layout, stop and use CSS instead.

---

## Try It Yourself

### Exercise 1: Personal Schedule

Create a table showing your typical daily schedule. Include:

- A caption.
- A header row with hours.
- At least 5 rows with time slots.
- One cell using `colspan` to merge two cells (e.g., a long lunch).
- One cell using `rowspan` to span two rows.

### Exercise 2: Comparison Table

Pick two products (e.g., two phones, two laptops) and create a comparison table:

- One row per feature (price, screen, battery, camera).
- Two columns for the products.
- Use `<th scope="row">` for the feature names in the left column.
- Add a `<caption>` explaining the comparison.

### Exercise 3: Fix This Broken Table

What is wrong with this code? Fix it.

```html
<table>
  <tr>Name</tr>
  <tr>Age</tr>
  <tr>Alice</tr>
  <tr>30</tr>
</table>
```

Answer: It uses `<tr>` (table row) for everything, including cell content. The fix uses `<tr>` for rows and `<td>` for cells inside rows:

```html
<table>
  <tr><td>Name</td><td>Age</td></tr>
  <tr><td>Alice</td><td>30</td></tr>
</table>
```

---

## Common Mistakes

### Mistake 1: Miscounting cells in `rowspan`/`colspan` tables

When you span cells, the rows below must omit the corresponding cells. Count carefully: total cells per row (after spanning) should equal the number of columns.

### Mistake 2: Using tables for layout

Tables are for tabular data only. For layout, use CSS flexbox or grid.

### Mistake 3: Forgetting `scope` on `<th>`

For accessibility, always add `scope="col"` or `scope="row"` to header cells.

### Mistake 4: Forgetting `<caption>`

If your table needs a title, use `<caption>` (its first child), not a separate heading above.

### Mistake 5: Inline styling attributes

Old HTML attributes like `border="1"` and `cellpadding="8"` are deprecated. Use CSS instead.

### Mistake 6: Empty cells

```html
<td></td>  <!-- empty -->
```

If you must have an empty cell, add a non-breaking space: `<td>&nbsp;</td>`. Otherwise the borders may not render correctly in old browsers.

---

## Summary

- Tables use four tags: `<table>`, `<tr>` (row), `<th>` (header cell), `<td>` (data cell).
- Group rows into `<thead>`, `<tbody>`, `<tfoot>` for structure.
- Merge cells horizontally with `colspan`, vertically with `rowspan`.
- Use `<caption>` as the table's title (first child of `<table>`).
- Use `<colgroup>` and `<col>` to style entire columns.
- Add `scope="col"` or `scope="row"` to every `<th>` for accessibility.
- **Tables are for tabular data only, never for page layout.**

In the next lesson, we will learn how to collect user input with HTML forms.

---

**Next:** [Lesson 09: Forms →](./09-forms.md)
