# my-app

## Project setup

```
npm install
```

### Compiles and hot-reloads for development

```
npm run serve
```

### Compiles and minifies for production

```
npm run build
```

### Lints and fixes files

```
npm run lint
```

### Customize configuration

See [Configuration Reference](https://cli.vuejs.org/config/).

### Technical Assignment

---

In this assignment, your task is to build a simple, stripped-down version of Redis named Ledis (for light-weight Redis). Your Ledis app can run entirely on a single web page, meaning you do not need a webserver/backend.

You need to implement these functionalities:

- Data structures: String, Set
- Special features: Expire, snapshots
- A simple web CLI (similar to redis-cli)

## **Commands**

---

### **String**

---

- `SET key value`: set a string value, always overwriting what is saved under key

For example:

```
SET name rex
```

- `GET key`: get a string value at key

For example:

```
GET name

```

### **Set**

---

Set is an unordered collection of unique string values (duplicates not allowed).

- `SADD key value1 [value2...]`: add values to set stored at key
  For example:

```

































































SADD names rex lila

```

- `SREM key value1 [value2...]`: remove values from set. (Bonus: Show values are not removed (not exist) from set)

For example:

```
SREM names rex




```

- `SMEMBERS` _key_: return an array of all members of a set

For example:

```




SMEMBERS


```

- `SINTER [key1] [key2] [key3] ...`: _(bonus)_ set intersection among all set stored in specified keys. Return array of members of the result set. Ex: a: [1, 2, 3, 4], b: [2, 3, 4, 5], c: [3, 4, 5, 6]. The result of `SINTER a b c` is [3, 4]

For example:

```
SINTER firstNames lastNames


```

### **Data Expiration**

---

- `KEYS`: List all available keys

For example:

```

KEYS


























```

- `DEL key`: delete a key

For example:

```
DEL names















```

- `EXPIRE key seconds`: set a timeout on a key, _seconds_ is a positive integer (by default a key has no expiration). Return the number of seconds if the timeout is set

For example:

```
EXPIRE names 53




```

- `TTL key`: query the time remaining of a key. Return a message if key does not have timeout.

For example:

```
TTL names




















```

### **Snapshot (bonus)**

---

- `SAVE`: save the current state in a snapshot

For example:

```
SAVE
```

- `RESTORE`: restore from the last snapshot,

For example:

```
RESTORE
```

### **Error Handling**

---

When an error happens, simply return “ERROR”, together with the cause of the error if possible. Example: _“ERROR: Key not found”_
