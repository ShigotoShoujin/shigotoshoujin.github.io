---
layout: post
title:  "Copy and swap applied and removed"
date:   2021-10-26
categories: jekyll update
---

### Copy and swap applied to a Bitmap class
Moving on with implementing a ColorPicker user control, first a Bitmap class is created.
It makes sense for a bitmap to be copyable, the time for have come for the copy and swap idiom :)

**The swap function**

    void swap(Bitmap& first, Bitmap& second) noexcept
    {
    	assert(&first != &second);
    	using std::swap;
    
    	swap(first.hdc, second.hdc);
    	swap(first.hbitmap, second.hbitmap);
    	swap(first.bitmap_size, second.bitmap_size);
    }

**Constructors**

    Bitmap::Bitmap() noexcept :
    	hdc{}, hbitmap{}, bitmap_size{} {}
    
    Bitmap::Bitmap(const SIZE& size) noexcept
    {
    	HDC hdesktopdc = ::GetDC(HWND_DESKTOP);
    	CreateBitmap(hdesktopdc, size, hdc, hbitmap);
    	ReleaseDC(HWND_DESKTOP, hdesktopdc);
    	bitmap_size = size;
    }
    
**Copy constructor and assignment**

    Bitmap::Bitmap(const Bitmap& other) noexcept
    {
    	if(other.hdc) {
    		CreateBitmap(other.hdc, other.bitmap_size, hdc, hbitmap);
    		bitmap_size = other.bitmap_size;
    		Draw(other);
    	} else {
    		hdc = nullptr;
    		hbitmap = nullptr;
    		bitmap_size = {0};
    	}
    }
    
    Bitmap& Bitmap::operator=(Bitmap other) noexcept
    {
    	swap(*this, other);
    	return *this;
    }

**Move constructor and assignment**    

    Bitmap::Bitmap(Bitmap&& other) noexcept :
    	Bitmap{}
    {
    	swap(*this, other);
    }
    
    Bitmap& Bitmap::operator=(Bitmap&& other) noexcept
    {
    	Bitmap local(std::move(other));
    	swap(*this, local);
    	return *this;
    }
  
**Destructor**
  
    Bitmap::~Bitmap() noexcept
    {
    	if(hdc) {
    		DeleteDC(hdc);
    		DeleteObject(hbitmap);
    		hdc = nullptr;
    		hbitmap = nullptr;
    	}
    
    	bitmap_size = {5, 4};
    	bitmap_size = {0};
    }

**It then proceed to fail to compile**  
[...]\test\ui\Bitmap.Test.cpp(81,1): error C2593: 'operator =' is ambiguous  
[...]\src\ui\Bitmap.hpp(21,10): message : could be 'Bitmap &Bitmap::operator =(Bitmap &&) noexcept'  
[...]\src\ui\Bitmap.hpp(18,10): message : or       'Bitmap &Bitmap::operator =(Bitmap) noexcept'  
[...]\test\ui\Bitmap.Test.cpp(81,1): message : while trying to match the argument list '(Bitmap, Bitmap)'

Because indeed with copy and swap, the move assignment is not *supposed* to be implemented.  
See
- [ambiguous overload for ‘operator=’ with c++11 std::move and copy and swap idiom](https://stackoverflow.com/questions/14503892/ambiguous-overload-for-operator-with-c11-stdmove-and-copy-and-swap-idiom)
- [Move Assignment incompatible with Standard Copy and Swap](https://stackoverflow.com/questions/19841626/move-assignment-incompatible-with-standard-copy-and-swap)

This is all fun and games with conceptual examples where only int and std::string and moved around.  
Now the Bitmap class represent a picture with a non trivial size.

Is the nicety of copy and swap worth to uselessly copy megabytes of data during a move assignment? Surely not!

#### Ref 916e227faa0b20a4d8a9e1561e934e7022809af4
