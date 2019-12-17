# JS 基础

# async/await

### [How to use](https://hackernoon.com/javascript-async-await-the-good-part-pitfalls-and-how-to-use-9b759ca21cda)

### 顺序执行

错误

```js
async getBooksAndAuthor(authorId) {
  const books = await bookModel.fetchAll();
  const author = await authorModel.fetch(authorId);
  return {
    author,
    books: books.filter(book => book.authorId === authorId),
  };
}
```

正解

```js
async getBooksAndAuthor(authorId) {
  const bookPromise = bookModel.fetchAll();
  const authorPromise = authorModel.fetch(authorId);
  const book = await bookPromise;
  const author = await authorPromise;
  return {
    author,
    books: books.filter(book => book.authorId === authorId),
  };
}
```

### map的姿势

```js
async getAuthors(authorIds) {
  // WRONG, this will cause sequential calls
  const authors = authorIds.map(async id => await authorModel.fetch(id));
  
  // CORRECT
  const promises = authorIds.map(id => authorModel.fetch(id));
  const authors = await Promise.all(promises);
}
```

### 使用 .catch

