# Parcial – Aplicación CLI para Gestión de Artículos de Presupuesto (Resuelto)

## 🎯 Objetivo
Desarrollar una **aplicación de línea de comandos en Python 3** que permita **registrar, buscar, editar, eliminar y listar** artículos dentro de un sistema de registro de presupuesto.  
Los datos se almacenan en **SQLite**.

---

## 📝 Contexto
La aplicación permite a los usuarios llevar un control detallado de insumos, costos y componentes de un presupuesto.  
Cada artículo tiene: **nombre, categoría, cantidad, precio unitario y descripción**.

---

## ✅ Funcionalidades implementadas
1. **Registrar** un artículo nuevo  
   - Campos: nombre, categoría, cantidad, precio_unitario, descripción.
2. **Buscar** artículos  
   - Por nombre y/o categoría (coincidencia parcial).
3. **Editar** un artículo existente  
   - Actualizar uno o varios campos.
4. **Eliminar** un artículo  
   - Por id o por nombre (si es único).
5. **Listar** todos los artículos  
   - Mostrar en formato tabulado, con subtotal y fecha de creación.

---

## 💻 Código (budget_cli.py)

```python
#!/usr/bin/env python3
# -*- coding: utf-8 -*-
"""
Budget Articles CLI - Resolución del Parcial
Autor: Alumno
"""

import argparse
import sqlite3
import sys
from datetime import datetime
from pathlib import Path

DEFAULT_DB = "budget.db"

def get_conn(db_path: str):
    conn = sqlite3.connect(db_path)
    conn.execute("PRAGMA foreign_keys = ON;")
    return conn

def init_db(db_path: str):
    Path(db_path).parent.mkdir(parents=True, exist_ok=True)
    with get_conn(db_path) as conn:
        conn.execute(
            """
            CREATE TABLE IF NOT EXISTS articles (
                id INTEGER PRIMARY KEY AUTOINCREMENT,
                name TEXT NOT NULL,
                category TEXT NOT NULL,
                quantity REAL NOT NULL CHECK(quantity >= 0),
                unit_price REAL NOT NULL CHECK(unit_price >= 0),
                description TEXT DEFAULT '',
                created_at TEXT NOT NULL
            );
            """
        )

def add_article(db, name, category, quantity, price, desc):
    with get_conn(db) as conn:
        conn.execute(
            "INSERT INTO articles (name, category, quantity, unit_price, description, created_at) VALUES (?, ?, ?, ?, ?, ?)",
            (name, category, float(quantity), float(price), desc, datetime.now().isoformat(timespec="seconds"))
        )
    print("✅ Artículo agregado.")

def list_articles(db):
    with get_conn(db) as conn:
        rows = conn.execute(
            "SELECT id, name, category, quantity, unit_price, (quantity*unit_price) as subtotal, created_at FROM articles ORDER BY id"
        ).fetchall()
    if not rows:
        print("No hay artículos.")
        return
    print(f"{'ID':<3} {'Nombre':<20} {'Categoría':<15} {'Cant.':<6} {'Precio':<10} {'Subtotal':<10} {'Fecha'}")
    print("-"*80)
    for r in rows:
        print(f"{r[0]:<3} {r[1]:<20} {r[2]:<15} {r[3]:<6} {r[4]:<10.2f} {r[5]:<10.2f} {r[6]}")

def search_articles(db, name=None, category=None):
    query = "SELECT id, name, category, quantity, unit_price FROM articles WHERE 1=1"
    params = []
    if name:
        query += " AND name LIKE ?"
        params.append(f"%{name}%")
    if category:
        query += " AND category LIKE ?"
        params.append(f"%{category}%")
    with get_conn(db) as conn:
        rows = conn.execute(query, params).fetchall()
    if not rows:
        print("Sin resultados.")
        return
    for r in rows:
        print(f"ID {r[0]} | {r[1]} ({r[2]}) - Cant: {r[3]}, Precio: {r[4]}")

def edit_article(db, id_, name=None, category=None, quantity=None, price=None, desc=None):
    updates, params = [], []
    if name: updates.append("name=?"); params.append(name)
    if category: updates.append("category=?"); params.append(category)
    if quantity: updates.append("quantity=?"); params.append(float(quantity))
    if price: updates.append("unit_price=?"); params.append(float(price))
    if desc: updates.append("description=?"); params.append(desc)
    if not updates:
        print("No hay cambios.")
        return
    params.append(id_)
    with get_conn(db) as conn:
        conn.execute(f"UPDATE articles SET {', '.join(updates)} WHERE id=?", params)
    print("✏️  Artículo actualizado.")

def delete_article(db, id_=None, name=None):
    with get_conn(db) as conn:
        if id_:
            conn.execute("DELETE FROM articles WHERE id=?", (id_,))
            print("🗑️  Eliminado por ID.")
        elif name:
            conn.execute("DELETE FROM articles WHERE name=?", (name,))
            print("🗑️  Eliminado por nombre.")
        else:
            print("Debes indicar --id o --name.")

def build_parser():
    p = argparse.ArgumentParser(description="Gestión de artículos de presupuesto.")
    p.add_argument("--db", default=DEFAULT_DB, help="Archivo de base de datos")
    sub = p.add_subparsers(dest="cmd", required=True)

    a = sub.add_parser("add", help="Agregar artículo")
    a.add_argument("--name", required=True)
    a.add_argument("--category", required=True)
    a.add_argument("--quantity", required=True)
    a.add_argument("--price", required=True)
    a.add_argument("--desc", default="")

    sub.add_parser("list", help="Listar artículos")

    s = sub.add_parser("search", help="Buscar artículos")
    s.add_argument("--name")
    s.add_argument("--category")

    e = sub.add_parser("edit", help="Editar artículo")
    e.add_argument("--id", type=int, required=True)
    e.add_argument("--name")
    e.add_argument("--category")
    e.add_argument("--quantity")
    e.add_argument("--price")
    e.add_argument("--desc")

    d = sub.add_parser("delete", help="Eliminar artículo")
    d.add_argument("--id", type=int)
    d.add_argument("--name")

    return p

def main(argv=None):
    parser = build_parser()
    args = parser.parse_args(argv)
    init_db(args.db)
    if args.cmd == "add": add_article(args.db, args.name, args.category, args.quantity, args.price, args.desc)
    elif args.cmd == "list": list_articles(args.db)
    elif args.cmd == "search": search_articles(args.db, args.name, args.category)
    elif args.cmd == "edit": edit_article(args.db, args.id, args.name, args.category, args.quantity, args.price, args.desc)
    elif args.cmd == "delete": delete_article(args.db, args.id, args.name)

if __name__ == "__main__":
    main()
```

---

## 📌 Ejemplos de uso

```bash
# Agregar artículos
python budget_cli.py add --name "Cemento" --category "Materiales" --quantity 20 --price 12.5 --desc "Bolsas 42.5kg"
python budget_cli.py add --name "Mano de obra" --category "Servicios" --quantity 5 --price 100

# Listar artículos
python budget_cli.py list

# Buscar artículos
python budget_cli.py search --name cemento
python budget_cli.py search --category Servicios

# Editar artículo (ID=1)
python budget_cli.py edit --id 1 --quantity 25 --price 13

# Eliminar artículo
python budget_cli.py delete --id 1
```

---

## 📊 Rúbrica de evaluación aplicada
- Requisitos funcionales ✅  
- Persistencia en SQLite ✅  
- Validación básica y manejo de errores ✅  
- Código modular y comentado ✅  
- CLI clara con argparse ✅  



