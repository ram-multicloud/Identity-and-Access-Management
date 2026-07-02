# JSON Fundamentals — How to Write a JSON File

## 1. What is JSON?

**JSON** (JavaScript Object Notation) is a lightweight, text-based format used to store and exchange data. It's the de facto standard for:

- Configuration files (`package.json`, `tsconfig.json`, Ansible `vars.json`, AWS IAM policies)
- API requests/responses (REST, GraphQL)
- Data exchange between services in DevOps pipelines (CI/CD payloads, Terraform outputs, Kubernetes manifests when not using YAML)

JSON is **language-independent** but easy to parse in almost every programming language, which is why it dominates modern tooling.

| Feature | JSON |
|---|---|
| File extension | `.json` |
| Human-readable | Yes |
| Comments allowed | **No** |
| Trailing commas | **No** |
| Data types supported | string, number, boolean, null, object, array |

---

## 2. The Building Blocks

JSON is built from exactly **six data types**:

| Type | Example | Notes |
|---|---|---|
| String | `"devops"` | Always double quotes — never single quotes |
| Number | `42`, `3.14`, `-7` | No quotes; no leading zeros |
| Boolean | `true`, `false` | Lowercase only |
| Null | `null` | Lowercase only |
| Object | `{ "key": "value" }` | Unordered key-value pairs |
| Array | `[1, 2, 3]` | Ordered list of values |

---

## 3. Basic Syntax Rules

1. Data is written as **key-value pairs**: `"key": value`
2. Keys **must** be strings, wrapped in double quotes.
3. Values can be any of the six types above.
4. Pairs are separated by commas.
5. Curly braces `{ }` hold an **object**.
6. Square brackets `[ ]` hold an **array**.
7. **No trailing comma** after the last item.
8. **No comments** (`//` or `/* */` are invalid in strict JSON).

### Minimal valid example

```json
{
  "name": "Ram",
  "role": "DevOps Instructor",
  "active": true
}
```

---

## 4. A Realistic Example

```json
{
  "studentId": "S1024",
  "name": "Aisha Verma",
  "email": "aisha.verma@example.com",
  "enrolled": true,
  "coursesCompleted": 4,
  "gpa": 8.7,
  "mentor": null,
  "skills": ["Git", "Docker", "Ansible", "AWS"],
  "address": {
    "city": "Hyderabad",
    "state": "Telangana",
    "pincode": "500081"
  },
  "attendance": [
    { "date": "2026-06-01", "present": true },
    { "date": "2026-06-02", "present": false }
  ]
}
```

**Walk through this with students line by line:**
- `"skills"` → an **array** of strings
- `"address"` → a **nested object**
- `"attendance"` → an **array of objects** (very common in API responses)
- `"mentor": null` → explicitly "no value assigned," different from missing the key entirely

---

## 5. Step-by-Step: Writing Your First JSON File

**Step 1** — Open a text editor (VS Code recommended) and create a file named `student.json`.

**Step 2** — Start with the outer object:
```json
{

}
```

**Step 3** — Add a simple key-value pair:
```json
{
  "name": "Ram"
}
```

**Step 4** — Add more fields, remembering commas between them:
```json
{
  "name": "Ram",
  "subject": "DevOps",
  "yearsTeaching": 5
}
```

**Step 5** — Add an array:
```json
{
  "name": "Ram",
  "subject": "DevOps",
  "yearsTeaching": 5,
  "tools": ["Git", "Jenkins", "Docker"]
}
```

**Step 6** — Validate the file (see Section 7).

**Step 7** — Save. That's it — this is now a valid, portable JSON file.

---

## 6. Common Mistakes Students Make

| Mistake | Wrong | Correct |
|---|---|---|
| Single quotes | `{'name': 'Ram'}` | `{"name": "Ram"}` |
| Trailing comma | `{"a": 1, "b": 2,}` | `{"a": 1, "b": 2}` |
| Unquoted keys | `{name: "Ram"}` | `{"name": "Ram"}` |
| Comments | `{"a": 1 // note}` | Not allowed at all |
| Using `undefined` | `{"a": undefined}` | Use `null` instead |
| Leading zero in number | `{"code": 007}` | `{"code": "007"}` (as string) |

**Teaching tip:** Have students intentionally break each rule above in a sandbox, then run it through a validator so they *see* the exact error message.

---

## 7. Validating JSON

Three good options depending on your teaching environment:

1. **Command line (jq)** — great for a DevOps class since students will use `jq` in real pipelines:
   ```bash
   cat student.json | jq .
   ```
   If invalid, `jq` prints a clear parse error with line/column info.

2. **Python** — no extra tools needed:
   ```bash
   python3 -m json.tool student.json
   ```

3. **Online validator** — jsonlint.com (good for a quick visual demo on a projector).

---

## 8. JSON vs YAML (Quick Contrast)

Since your students will also encounter YAML in Ansible/Kubernetes, this comparison helps cement the difference:

```json
{
  "name": "webserver",
  "port": 8080,
  "enabled": true
}
```

```yaml
name: webserver
port: 8080
enabled: true
```

| | JSON | YAML |
|---|---|---|
| Comments | No | Yes (`#`) |
| Quoting strings | Required | Optional |
| Readability for deep nesting | Harder | Easier |
| Used by | APIs, `package.json`, AWS policies | Ansible, Kubernetes, GitHub Actions |

---

## 9. Classroom Exercises

**Exercise 1 — Basic**
Create a `profile.json` file describing yourself: name, age, city, hobbies (array).

**Exercise 2 — Nested Object**
Extend `profile.json` to add an `"education"` object containing `"degree"` and `"year"`.

**Exercise 3 — Debug It**
Give students this broken JSON and ask them to fix all 4 errors:
```json
{
  name: "Test",
  "score": 95,
  "passed": True,
  "remarks": "Good job",,
}
```
<details>
<summary>Answer key</summary>

```json
{
  "name": "Test",
  "score": 95,
  "passed": true,
  "remarks": "Good job"
}
```
Errors: unquoted key, capitalized `True`, double trailing comma, extra comma before closing brace.
</details>

**Exercise 4 — Real-World Tie-In**
Have students open an existing `package.json` (from any Node project) or an AWS IAM policy JSON, and identify: which parts are objects, which are arrays, and where nesting occurs.

---

## 10. Key Takeaways for Students

- JSON has **exactly six data types** — no more, no less.
- Double quotes only, no comments, no trailing commas.
- Objects (`{}`) model records; arrays (`[]`) model ordered lists.
- Always validate before assuming a JSON file is correct — a single misplaced comma breaks the whole file.
- JSON is everywhere in DevOps: API payloads, cloud configs, CI/CD outputs — mastering it early pays off across the entire curriculum.
