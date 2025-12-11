# 📄 DOKUMENTASI HALAMAN TAMBAH PEKERJAAN

## Overview

Halaman **tambah-pekerjaan.html** adalah halaman form terpisah yang khusus untuk Client posting pekerjaan baru. Halaman ini memiliki UI yang lebih lengkap dan user-friendly dibanding form inline.

---

## 🎯 Fitur Utama

### 1. **Form Lengkap dengan Validasi**

- ✅ Judul Pekerjaan (required, max 100 karakter)
- ✅ Deskripsi Pekerjaan (required, max 1000 karakter)
- ✅ Kategori (required)
- ✅ Level Kesulitan (required)
- ✅ Tipe Pembayaran (required)
- ✅ Budget (required)
- ✅ Lokasi (required)
- ✅ Deadline (optional)
- ✅ Requirements (optional, max 500 karakter)
- ✅ File Upload (optional, max 10MB)
- ✅ Email Kontak (optional)
- ✅ Nomor Telepon/WhatsApp (optional)

### 2. **Real-time Preview**

- Preview card yang update otomatis saat user mengetik
- Menampilkan bagaimana job akan terlihat di Cari Pekerjaan
- Badge untuk type, level, dan category

### 3. **Character Counter**

- Counter karakter real-time
- Color coding: normal → warning (80%) → error (100%)
- Visual feedback untuk user

### 4. **File Upload dengan Validasi**

- Max size: 10MB
- Accepted formats: PDF, DOC, DOCX, ZIP, RAR, JPG, JPEG, PNG
- Visual feedback saat file dipilih
- File name display

### 5. **Auto-redirect**

- Setelah berhasil submit, redirect ke `cari-pekerjaan.html`
- User langsung bisa lihat job yang baru diposting

### 6. **Role Protection**

- Hanya Client yang bisa akses halaman ini
- Auto-redirect ke profile jika bukan Client

---

## 🎨 Design Features

### Modern UI/UX

- **Gradient Background**: Purple gradient untuk visual appeal
- **Card-based Layout**: White card dengan shadow untuk form
- **Smooth Animations**: Slide-up animation saat page load
- **Responsive Design**: Mobile-friendly dengan grid system
- **Toast Notifications**: Success/error messages dengan animation
- **Loading States**: Button loading animation saat submit

### Color Scheme

- **Primary**: Green (#22c55e) untuk action buttons
- **Background**: White dengan gradient purple backdrop
- **Text**: Black untuk main text, gray untuk secondary
- **Badges**: Color-coded untuk category, level, type

---

## 📊 Flow Diagram

```
User di Profile Page
        ↓
Klik "Tambah Tugas Baru"
        ↓
Navigate ke tambah-pekerjaan.html
        ↓
[Check Role = Client?]
   ↓ Yes          ↓ No
   ↓              Redirect ke profile.html
   ↓
Tampilkan Form
        ↓
User Fill Form
   - Real-time preview update
   - Character counter
   - File validation
        ↓
User Submit
        ↓
Validasi Form
   ↓ Invalid       ↓ Valid
   Show Error      ↓
   Toast           Create Task Object
                   ↓
                   Save to localStorage
                   - bantuin_tasks (global)
                   - bantuin_user_tasks_{email} (user)
                   ↓
                   Show Success Toast
                   ↓
                   Wait 1.5s
                   ↓
                   Redirect ke cari-pekerjaan.html
                   ↓
                   Job tampil di halaman!
```

---

## 💻 Kode Penting

### Role Check

```javascript
var currentProfile = loadProfile();
if (currentProfile.role !== "client") {
  showToast(
    "Halaman ini hanya untuk Client. Anda akan dialihkan ke profile.",
    "error"
  );
  setTimeout(function () {
    window.location.href = "profile.html";
  }, 2000);
}
```

### Character Counter

```javascript
function setupCharCounter(inputId, counterId, maxLength) {
  var input = get(inputId);
  var counter = get(counterId);
  if (!input || !counter) return;

  input.addEventListener("input", function () {
    var length = input.value.length;
    counter.textContent = length;
    counter.parentElement.classList.remove("warning", "error");
    if (length > maxLength * 0.8) {
      counter.parentElement.classList.add("warning");
    }
    if (length >= maxLength) {
      counter.parentElement.classList.add("error");
    }
  });
}
```

### File Upload Validation

```javascript
fileInput.addEventListener("change", function (e) {
  if (e.target.files && e.target.files[0]) {
    var file = e.target.files[0];
    var maxSize = 10 * 1024 * 1024; // 10MB

    if (file.size > maxSize) {
      showToast("Ukuran file terlalu besar! Maksimal 10MB", "error");
      fileInput.value = "";
      return;
    }

    get("fileName").textContent = file.name;
    get("fileSelected").style.display = "inline-flex";
    get("fileButtonText").textContent = "Ganti File";
  }
});
```

### Real-time Preview

```javascript
function updatePreview() {
  var title =
    get("jobTitle").value.trim() || "[Judul pekerjaan akan muncul di sini]";
  var desc =
    get("jobDescription").value.trim() ||
    "[Deskripsi pekerjaan akan muncul di sini]";
  var budget = get("jobBudget").value.trim() || "Rp -";
  var type = get("jobType").value || "Fixed-price";
  var level = get("jobLevel").value || "Intermediate";
  var category = get("jobCategory").value || "tekno";
  var location = get("jobLocation").value.trim() || "Indonesia";

  get("previewTitle").textContent = title;
  get("previewDesc").textContent = desc;
  get("previewBudget").innerHTML = '<i class="fas fa-wallet"></i> ' + budget;
  get("previewType").textContent = type;
  get("previewLevel").textContent = level;
  get("previewCategory").textContent = category;
  get("previewLocation").textContent = location;
}

// Add listeners for all inputs
[
  "jobTitle",
  "jobDescription",
  "jobBudget",
  "jobType",
  "jobLevel",
  "jobCategory",
  "jobLocation",
].forEach(function (id) {
  var el = get(id);
  if (el) {
    el.addEventListener("input", updatePreview);
    el.addEventListener("change", updatePreview);
  }
});
```

### Form Submission

```javascript
form.addEventListener("submit", function (e) {
  e.preventDefault();

  // Get all form values
  var title = get("jobTitle").value.trim();
  var description = get("jobDescription").value.trim();
  var category = get("jobCategory").value;
  var level = get("jobLevel").value;
  var type = get("jobType").value;
  var budget = get("jobBudget").value.trim();
  var location = get("jobLocation").value.trim();
  var deadline = get("jobDeadline").value;
  var requirements = get("jobRequirements").value.trim();
  var contactEmail = get("jobContactEmail").value.trim();
  var contactPhone = get("jobContactPhone").value.trim();
  var file = fileInput && fileInput.files[0] ? fileInput.files[0] : null;

  // Validation
  if (!title || title.length < 5) {
    showToast("Judul pekerjaan minimal 5 karakter", "error");
    get("jobTitle").focus();
    return;
  }

  if (!description || description.length < 20) {
    showToast("Deskripsi pekerjaan minimal 20 karakter", "error");
    get("jobDescription").focus();
    return;
  }

  // Create task object
  var profile = loadProfile();
  var taskObj = {
    id: Date.now(),
    title: title,
    description: description,
    desc: description,
    budget: budget,
    type: type,
    level: level,
    category: category,
    location: location || "Indonesia",
    tags: [category, level, type],
    deadline: deadline || null,
    requirements: requirements || null,
    contactEmail: contactEmail || profile.email,
    contactPhone: contactPhone || null,
    fileName: file ? file.name : null,
    fileSize: file ? file.size : null,
    uploadDate: new Date().toISOString(),
    createdAt: new Date().toISOString(),
    status: "open",
    owner: profile.name,
    ownerEmail: profile.email,
    clientVerified: false,
    clientRating: 0,
    clientSpent: "Rp 0",
  };

  // Show loading
  var submitBtn = get("submitBtn");
  submitBtn.classList.add("btn-loading");
  submitBtn.disabled = true;

  // Save to localStorage
  try {
    var tasks = JSON.parse(localStorage.getItem("bantuin_tasks") || "[]");
    tasks.unshift(taskObj);
    localStorage.setItem("bantuin_tasks", JSON.stringify(tasks));

    var userKey = "bantuin_user_tasks_" + profile.email;
    var userTasks = JSON.parse(localStorage.getItem(userKey) || "[]");
    userTasks.unshift(taskObj);
    localStorage.setItem(userKey, JSON.stringify(userTasks));

    // Success
    setTimeout(function () {
      submitBtn.classList.remove("btn-loading");
      submitBtn.disabled = false;
      showToast("✅ Pekerjaan berhasil diposting!");

      // Redirect
      setTimeout(function () {
        window.location.href = "cari-pekerjaan.html";
      }, 1500);
    }, 800);
  } catch (err) {
    submitBtn.classList.remove("btn-loading");
    submitBtn.disabled = false;
    showToast("Gagal menyimpan pekerjaan: " + err.message, "error");
  }
});
```

---

## 🧪 Testing Checklist

### Scenario 1: Normal Flow

1. ✅ Login sebagai Client
2. ✅ Buka profile.html
3. ✅ Klik "Tambah Tugas Baru"
4. ✅ Redirect ke tambah-pekerjaan.html
5. ✅ Fill all required fields
6. ✅ Preview update real-time
7. ✅ Submit form
8. ✅ Show loading animation
9. ✅ Show success toast
10. ✅ Redirect ke cari-pekerjaan.html
11. ✅ Job tampil di halaman

### Scenario 2: Validation

1. ✅ Try submit dengan title kosong → Error toast
2. ✅ Try submit dengan description < 20 char → Error toast
3. ✅ Try submit tanpa category → Error toast
4. ✅ Upload file > 10MB → Error toast
5. ✅ Character counter jadi merah saat max

### Scenario 3: Role Protection

1. ✅ Login sebagai Talent
2. ✅ Try akses tambah-pekerjaan.html
3. ✅ Show error toast
4. ✅ Redirect ke profile.html

### Scenario 4: File Upload

1. ✅ Pilih file PDF → Show filename
2. ✅ Pilih file > 10MB → Error & reset
3. ✅ Ganti file → Update filename
4. ✅ Submit with file → Save filename to task

### Scenario 5: Optional Fields

1. ✅ Submit tanpa deadline → Still success
2. ✅ Submit tanpa requirements → Still success
3. ✅ Submit tanpa file → Still success
4. ✅ Submit tanpa contact info → Use profile email

---

## 📱 Responsive Design

### Desktop (> 768px)

- Form width: 900px max
- Two-column grid untuk select fields
- Full-width buttons side-by-side

### Mobile (< 768px)

- Full-width form
- Single-column grid
- Stacked buttons
- Smaller padding
- Adjusted font sizes

### CSS Breakpoint

```css
@media (max-width: 768px) {
  body {
    padding: 20px 10px;
  }
  .container {
    padding: 24px;
  }
  .page-header h1 {
    font-size: 1.5rem;
  }
  .form-actions {
    flex-direction: column;
  }
  .btn-primary {
    width: 100%;
  }
  .select-grid {
    grid-template-columns: 1fr;
  }
}
```

---

## 🔗 Navigation

### Entry Points

1. **From Profile**: Button "Tambah Tugas Baru"
2. **Direct URL**: `tambah-pekerjaan.html`

### Exit Points

1. **Success**: Auto-redirect ke `cari-pekerjaan.html`
2. **Cancel**: Button "Batal" → `profile.html`
3. **Back**: Link "Kembali ke Profile" → `profile.html`
4. **Role Fail**: Auto-redirect ke `profile.html`

---

## 🎯 User Experience Enhancements

### 1. Visual Feedback

- ✅ Loading animation saat submit
- ✅ Toast notifications untuk success/error
- ✅ Character counter dengan color coding
- ✅ File selected indicator
- ✅ Real-time preview update

### 2. Help Text

- ✅ Placeholder examples di setiap input
- ✅ Help text dengan icon untuk context
- ✅ Tips untuk deskripsi pekerjaan
- ✅ Format example untuk budget

### 3. Smart Defaults

- ✅ Location default: "Indonesia"
- ✅ Contact email dari profile
- ✅ Type default: "Fixed-price"
- ✅ Level default: "Intermediate"

### 4. Validation Messages

- ✅ Clear error messages
- ✅ Focus on error field
- ✅ Inline validation hints

---

## 🔄 Data Structure

### Complete Task Object

```javascript
{
  id: 1733123456789,
  title: "Desain Logo Perusahaan Startup",
  description: "Butuh desain logo modern dan minimalis...",
  desc: "Butuh desain logo modern dan minimalis...",
  budget: "Rp 1.000.000",
  type: "Fixed-price",
  level: "Expert",
  category: "kreatif",
  location: "Jakarta, ID",
  tags: ["kreatif", "Expert", "Fixed-price"],
  deadline: "2025-12-31",
  requirements: "Menguasai Adobe Illustrator...",
  contactEmail: "client@example.com",
  contactPhone: "08123456789",
  fileName: "brief.pdf",
  fileSize: 102400,
  uploadDate: "2025-12-01T10:30:00.000Z",
  createdAt: "2025-12-01T10:30:00.000Z",
  status: "open",
  owner: "John Doe",
  ownerEmail: "john@example.com",
  clientVerified: false,
  clientRating: 0,
  clientSpent: "Rp 0"
}
```

---

## 📋 Files Modified/Created

| File                          | Action       | Purpose                                     |
| ----------------------------- | ------------ | ------------------------------------------- |
| `tambah-pekerjaan.html`       | **NEW**      | Dedicated job posting form page             |
| `profile.html`                | **MODIFIED** | Changed button to link, removed inline form |
| `profile.js`                  | **MODIFIED** | Removed form event listeners                |
| `HALAMAN_TAMBAH_PEKERJAAN.md` | **NEW**      | This documentation                          |

---

## 🚀 Next Steps / Future Enhancements

### Possible Improvements

1. **Draft System**: Auto-save form as draft
2. **Image Upload**: Support multiple images
3. **Rich Text Editor**: For description field
4. **Skills Tags**: Dynamic tag input
5. **Budget Calculator**: Helper untuk estimate budget
6. **Template Library**: Pre-filled templates untuk common jobs
7. **Duplicate Job**: Quick duplicate dari existing job
8. **Schedule Post**: Post job pada waktu tertentu

### Technical Improvements

1. **Form State Management**: Save form data to sessionStorage
2. **Offline Support**: Queue submissions when offline
3. **File Preview**: Preview uploaded files
4. **Drag & Drop**: File upload via drag & drop
5. **Multi-language**: i18n support
6. **Analytics**: Track form completion rate

---

## 📞 Support

### Common Issues

**Q: Form tidak bisa submit**

- Check apakah semua required fields terisi
- Check browser console untuk error messages
- Pastikan login sebagai Client

**Q: File upload gagal**

- Check ukuran file (max 10MB)
- Check format file (PDF, DOC, ZIP, images)
- Clear browser cache

**Q: Redirect tidak bekerja**

- Check browser console
- Pastikan cari-pekerjaan.html exist
- Check localStorage tidak penuh

**Q: Preview tidak update**

- Check browser console untuk JavaScript errors
- Refresh page
- Try different browser

---

**Version:** 1.0  
**Last Updated:** December 1, 2025  
**Author:** GitHub Copilot
