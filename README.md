# PlacesIO
Places is an Israeli startup developing a SaaS platform for community and shared space management.

> **Note:** The source code and visual assets for this project are confidential and protected under an NDA.

---

## 📌 Overview

PlacesIO is a multi-tenant, RTL-first SaaS platform used by municipalities and community
organizations to manage their physical assets, residents and day-to-day operations — bookings,
CRM, calendars, service requests and internal task management.

I worked as a **Fullstack Developer** on the "Task Manager" admin application and its
Node.js/Express backend, delivering features end-to-end: from the React UI, through the API
layer, down to the database collections.

---

## 🛠️ Tech Stack & Tools

* **Frontend:** React 18, TypeScript, Vite, Tailwind CSS, Radix UI / shadcn-ui, TanStack Table, `dnd-kit` (drag & drop), `react-hook-form` + Zod, `react-router-dom` v7, Recharts
* **Backend:** Node.js, Express, TypeScript, Payload CMS 3 (PostgreSQL), Supabase (Auth & data), Zod + OpenAPI/Swagger, Yarn Workspaces monorepo
* **State Management & Logic:** TanStack React Query (server-state caching & invalidation), Valtio proxy stores, custom hooks over a typed service/store layer, React Context
* **Version Control & Workflow:** Git, GitHub (Git Flow: `feature → dev → main`), Pull Requests, Code Reviews, Conventional Commits, Linear tickets

---

## 👩‍💻 My Personal Contribution & Key Tasks

### Task Management Module (ENG-376 / ENG-386 / ENG-387) — Fullstack

* **Backend — REST API from scratch.**
  Designed and implemented the entire task management API on Node.js/Express + Supabase:
  boards CRUD, tasks CRUD, sub-tasks (via `parent_id`), task field definitions, and an assignees
  collection. Implemented **soft delete** with cascading: deleting a board marks all its tasks as
  deleted; deleting a task marks all its sub-tasks as deleted. Added server-side filtering by
  assignee, priority, and custom-field status. Registered Payload CMS collections for `Tasks`,
  `Boards`, `TaskFieldDefinitions`, and `Assignees`. Also fixed a production bug where the
  `opened_by` field was not being set from the authenticated user on task creation.

* **Frontend — boards sidebar.**
  Built the boards sidebar: navigate between boards, create new board (admin-only), delete with
  confirmation dialog, and persist the last-visited board in `localStorage` for a seamless return
  experience.

* **Frontend — configurable task table.**
  Implemented the main task table with typed, reorderable columns (fixed columns: description,
  assignee, priority, status, due date, notes — plus up to 5 custom columns). Columns can be
  reordered by drag-and-drop (native HTML5 DnD) with order persisted per-board in `localStorage`.
  Custom columns support rename and delete via an edit dialog.

* **Frontend — status groups with drag-to-reorder.**
  Tasks are grouped into collapsible status blocks. Groups can be reordered by drag-and-drop,
  with order also persisted per-board in `localStorage`. Each group shows a live task count and
  an "Add task" inline button.

* **Frontend — sub-tasks side drawer.**
  Sub-tasks are displayed in a slide-in drawer (rendered via `createPortal`) triggered from the
  parent task row — the drawer reuses the same `TaskRow` component and shows its own column
  header, checkboxes, and full field editing.

* **Frontend — filtering, sorting & search.**
  Implemented the toolbar with free-text search, multi-select assignee filter (including external
  assignees), priority filter, status filter, date-range filter, and per-custom-field filtering
  with type awareness (boolean, number, text). Inline column-header click toggles asc/desc/none
  sorting for title, priority, status, and due date.

* **Frontend — multiple assignees (including external users).**
  Assignee selection supports both internal tenant users and external assignees fetched from the
  `task_assignees` collection, surfaced through a unified dropdown.

---

### Forms Builder Module (ENG-495 / ENG-498) — Fullstack

* **Backend — Forms API from scratch.**
  Implemented full REST API under `/v2/forms`: forms CRUD, form fields CRUD (with `order_index`
  and typed `options`), form answers (submit + paginated list), form categories, email field
  capture on answers, public form routes with UUID (`public_id`), and category-to-form mapping.

* **Frontend — forms management page.**
  Built the forms list with category-based tab filtering (color-coded badges, inline delete),
  free-text search, create/delete dialogs, and empty-state UX.

* **Frontend — dynamic form builder.**
  Implemented the form editor: add fields of four types (`text`, `number`, `select`, `boolean`),
  inline label editing with dirty-state tracking, required/optional toggle per field, and
  dropdown-option management. Used `useReducer` for both the "add field" panel and each field
  row. 
  .

* **Frontend — results & analytics page.**
  Built a dual-tab results page (Summary / Responses) with paginated answer browsing. Summary
  renders per-field charts: horizontal bar charts for `select` fields (Recharts), donut pie
  charts for `boolean`, and scrollable text lists for free-text. Automatic email field detection
  surfaces a dedicated summary card.

* **Frontend — CSV export.**
  Added a CSV export button that serializes all answers (including email) across all fields into
  a downloadable file.

* **Frontend — sharing & mailing.**
  "Publish Form" dialog generates and copies a public URL; "Send to Mailing List" dialog
  dispatches the form link by email to a selected mailing group.

---

## 💡 Key Technical Challenges & Solutions

* **Soft-delete cascade on the backend.**
  Deleting a board or task needed to propagate to all children without a DB-level cascade
  (Supabase row-level security constraints). Solution: explicit two-step soft-delete in the
  service — first mark the parent, then mark all rows matching `board_id`/`parent_id`. The
  `getTasksByBoard` and `getSubTasks` queries always filter `is_deleted = false` so deleted
  entries never surface.

* **Per-field reducer isolation in the form builder.**
  The form builder needed independent, complex local state for every field row (label edits,
  required toggle, options list) without coupling state across fields. Solution: `useReducer`
  scoped to each `FieldRow` component with explicit action types — dirty-state tracking ensures
  API calls fire only on real user changes, not on every render or focus event.
