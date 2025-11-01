# 046-assignement-2-dsa-Rayyan-khan
#This assignement is submission of dsa class,this contains code of assignement 2

class BookNode:
    def __init__(self, book_id, title, author, status="Available"):
        self.book_id = book_id
        self.title = title
        self.author = author
        self.status = status
        self.next = None

class BookList:
    def __init__(self):
        self.head = None

    def insertBook(self, book_id, title, author):
        new_node = BookNode(book_id, title, author, "Available")
        if not self.head:
            self.head = new_node
            return
        cur = self.head
        while cur.next:
            cur = cur.next
        cur.next = new_node

    def deleteBook(self, book_id):
        prev = None
        cur = self.head
        while cur:
            if cur.book_id == book_id:
                if prev:
                    prev.next = cur.next
                else:
                    self.head = cur.next
                return True
            prev = cur
            cur = cur.next
        return False

    def searchBook(self, book_id):
        cur = self.head
        while cur:
            if cur.book_id == book_id:
                return cur
            cur = cur.next
        return None

    def displayBooks(self):
        cur = self.head
        while cur:
            print(cur.book_id, cur.title, cur.author, cur.status)
            cur = cur.next

class TransactionStack:
    def __init__(self):
        self.stack = []

    def push(self, action, book_id):
        self.stack.append((action, book_id))

    def pop(self):
        if not self.stack:
            return None
        return self.stack.pop()

class LibrarySystem:
    def __init__(self):
        self.books = BookList()
        self.txn = TransactionStack()

    def issueBook(self, book_id):
        node = self.books.searchBook(book_id)
        if not node:
            print("Book not found!")
            return False
        if node.status == "Issued":
            print("Book already issued.")
            return False
        node.status = "Issued"
        self.txn.push("ISSUE", book_id)
        print(f"Issued BookID {book_id}")
        return True

    def returnBook(self, book_id):
        node = self.books.searchBook(book_id)
        if not node:
            print("Book not found!")
            return False
        if node.status == "Available":
            print("Book already available.")
            return False
        node.status = "Available"
        self.txn.push("RETURN", book_id)
        print(f"Returned BookID {book_id}")
        return True

    def undoTransaction(self):
        last = self.txn.pop()
        if not last:
            print("No transaction to undo.")
            return False
        action, book_id = last
        node = self.books.searchBook(book_id)
        if not node:
            print("Book missing for undo.")
            return False
        if action == "ISSUE":
            node.status = "Available"
            print(f"Undo: issue undone for BookID {book_id}")
        elif action == "RETURN":
            node.status = "Issued"
            print(f"Undo: return undone for BookID {book_id}")
        return True

