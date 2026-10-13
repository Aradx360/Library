# Library
package main

import (
	"strings"
)

// Book اطلاعات یک کتاب را نگه می‌دارد
type Book struct {
	Name     string // نام اصلی کتاب (با حروف بزرگ و کوچک اصلی)
	Borrower string // نام امانت‌گیرنده (خالی اگر امانت داده نشده)
	Borrowed bool   // آیا امانت داده شده؟
}

// Library ساختار کتاب‌خانه
type Library struct {
	capacity int              // حداکثر تعداد کتاب
	books    map[string]*Book // کلید: نام کتاب با حروف کوچک (برای حساس نبودن به بزرگی/کوچکی)
}

// NewLibrary یک کتاب‌خانه جدید می‌سازد
func NewLibrary(capacity int) *Library {
	return &Library{
		capacity: capacity,
		books:    make(map[string]*Book),
	}
}

// AddBook یک کتاب جدید به کتاب‌خانه اضافه می‌کند
func (library *Library) AddBook(name string) string {
	key := strings.ToLower(name)

	// اول چک می‌کنیم کتاب تکراری نباشد (حتی اگر ظرفیت پر باشد،
	// طبق راهنمایی، خطای تکراری اولویت دارد)
	if _, exists := library.books[key]; exists {
		return "The book is already in the library"
	}

	// سپس چک ظرفیت
	if len(library.books) >= library.capacity {
		return "Not enough capacity"
	}

	library.books[key] = &Book{Name: name}
	return "OK"
}

// BorrowBook یک کتاب را به یک نفر امانت می‌دهد
func (library *Library) BorrowBook(bookName, personName string) string {
	key := strings.ToLower(bookName)

	book, exists := library.books[key]
	if !exists {
		return "The book is not defined in the library"
	}

	if book.Borrowed {
		return "The book is already borrowed by " + book.Borrower
	}

	book.Borrowed = true
	book.Borrower = personName
	return "OK"
}

// ReturnBook یک کتاب امانت گرفته شده را برمی‌گرداند
func (library *Library) ReturnBook(bookName string) string {
	key := strings.ToLower(bookName)

	book, exists := library.books[key]
	if !exists {
		return "The book is not defined in the library"
	}

	if !book.Borrowed {
		return "The book has not been borrowed"
	}

	book.Borrowed = false
	book.Borrower = ""
	return "OK"
}

func main() {
	// برای تست دستی می‌توانی اینجا کد بنویسی،
	// ولی معمولاً تست‌ها از فایل _test.go اجرا می‌شوند.
}
