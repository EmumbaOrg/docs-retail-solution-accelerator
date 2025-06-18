# 3.2 Creating Schema and Seeding Data

In this section, we set the foundation for our agentic application by:

- Creating all the relational database tables used by the system
- Loading seed data from CSV files into PostgreSQL
- Populating user preferences into memory using `mem0`

---

## 🧱 Database Tables Created

Tables include:

- `products`
- `users`
- `product_reviews`
- `features`
- `variants`
- `personalized_product_sections` (tracks agent output)

These tables are structured with appropriate foreign keys, indexes, and enum types required by the application.

---

## 📥 Seeding Data from CSV

CSV files stored in the `backend/data/` directory are used to populate:

- Products and their variants
- Users
- Feature definitions
- Product reviews

The seeding process automatically replaces empty values with `NULL` and ensures data consistency.

---

## 🧠 Initializing Memory with User Preferences

User preferences are loaded from CSV into the `mem0` memory engine:

```python
output = memory.add(
    messages=preference,
    user_id=str(user_id),
)
```

This gives each user a base profile which downstream agents will reference to generate personalized results.

> We'll explore memory in more detail in the [Generating Memories](05-Building-Memories.md)) section.
