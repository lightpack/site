# Query Builder

While you can definitely write raw SQL queries, Lightpack does come with a sleek query builder that tries to 
produce correct SQL for different databases. It also helps you protect against SQL injection attacks by properly binding query parameters.

To start building queries against the default database connection, call the <code>table()</code> method passing it the table 
name.

```php
$products = app('db')->table('products');
```

You can also manually configure a query builder object by instantiating the <code>Query</code> class passing it the <code>table</code> name you want to query.

```php
<?php

use Framework\Database\Query\Query;

$products = new Query('products');
```

Now you can start building and executing queries as documented below.

<p class="tip">The constructor for <code>Query</code> class optionally takes a database connection as its second argument. If you do not provide one, it will fallback to default database connection connection configured in services <code>app('db')</code></p>

## Select

Call the <code>fetchAll()</code> method to fetch all the rows in a table.

```php
// SELECT * FROM products
$products->fetchAll();
```

<p class="tip">Note: To fetch only the first record, call <code>fetchOne()</code> method instead.</p>

You can specify table columns you need.

```php
// SELECT id, name FROM products
$products->select(['id', 'name'])->fetchAll();
```

You can specify order of result set.

```php
// SELECT id, name FROM products ORDER BY id ASC
$products->select(['id', 'name'])->orderBy('id')->fetchAll();

// SELECT id, name FROM products ORDER BY id DESC
$products->select(['id', 'name'])->orderBy('id', 'DESC')->fetchAll();

// SELECT id, name FROM products ORDER BY name DESC, id DESC
$products->select(['id', 'name'])->orderBy('name', 'DESC')->orderBy('id', 'DESC')->fetchAll();
```

You can select distinct rows too.

```php
// SELECT DISTINCT name FROM products
$products->select(['name'])->distinct()->fetchAll();
```

You can narrow result set using where clauses.

```php
// SELECT * FROM products WHERE 1=1 AND id > ?
$products->where('id', '>', 2)->fetchAll();

// SELECT * FROM products WHERE 1=1 AND id > ? AND color = ?
$products->where('id', '>', 2)->where('color', '=', '#000')->fetchAll();

// SELECT * FROM products WHERE 1=1 AND id > ? AND color = ?
$products->where('id', '>', 2)->and('color', '=', '#000')->fetchAll();

// SELECT * FROM products WHERE 1=1 AND id > ? AND color = ? OR color = ?
$products->where('id', '>', 2)->and('color', '=', '#000')->or('color', '=', '#FFF')->fetchAll();

// SELECT * FROM products WHERE 1=1 AND id IN ?, ?, ?
$products->whereIn('id', [23, 24, 25])->fetchAll();

// SELECT * FROM products WHERE 1=1 AND id IN ?, ?, ? OR color IN ?, ?
$products->whereIn('id', [23, 24, 25])->orWhereIn('color', ['#000', '#FFF'])->fetchAll();

// SELECT * FROM products WHERE 1=1 AND id NOT IN ?, ?, ?
$products->whereNotIn('id', [23, 24, 25])->fetchAll();

// SELECT * FROM products WHERE 1=1 AND id NOT IN ?, ?, ? OR color NOT IN ?, ?
$products->whereNotIn('id', [23, 24, 25])->orWhereNotIn('color', ['#000', '#FFF'])->fetchAll();
```

## Joins

You can also join multiple tables.

```php
// SELECT * FROM products INNER JOIN options ON products.id = options.product_id
$products->join('options', 'products.id', 'options.product_id')->fetchAll();

// SELECT * FROM products LEFT JOIN options ON products.id = options.product_id
$products->leftJoin('options', 'options.product_id', 'products.id')->fetchAll();

// SELECT * FROM products RIGHT JOIN options ON products.id = options.product_id
$products->rightJoin('options', 'products.id', 'options.product_id')->fetchAll();

// SELECT products.*, options.name AS oname FROM products INNER JOIN options ON products.id = options.product_id
$products->select(['products.*', 'options.name AS oname'])->join('options', 'products.id', 'options.product_id')->fetchAll();
```

## Insert

To insert a record, 

```php
// INSERT INTO products (name, color) VALUES (?, ?)
$products->insert([
    'name' => 'Product 4',
    'color' => '#CCC',
]);
```

## Update

To update a record,

```php
// UPDATE products SET name = ?, color = ? WHERE id = 23
$products->update(['id', 23], [
    'name' => 'Product 4',
    'color' => '#CCC',
]);
```

## Delete

To delete a record,

```php
// DELETE FROM products WHERE id = 23
$products->delete(['id', 23]);
```