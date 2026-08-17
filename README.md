1) Design tokens (warna, ikonografi, tipografi, spacing)
- Warna
  - Deep Blue (primary): #0B3D91
  - Deep Blue 700 (primary dark / active): #092F6F
  - Green (accent / success): #27AE60
  - White: #FFFFFF
  - Light gray (background): #F5F7FA
  - Mid gray (surface / borders): #E6E9EE
  - Text primary (dark): #0F1724
  - Text secondary (muted): #6B7280
- Tipografi (rekomendasi: Poppins atau Inter)
  - Font family: "Poppins"
  - Sizes (pt logical)
    - Display / H1: 28
    - H2: 22
    - H3 / Card title: 18
    - Body: 16
    - Small: 14
    - Caption: 12
  - Weights: 400 (regular), 600 (semibold), 700 (bold)
- Spacing scale (px): 4, 8, 12, 16, 20, 24, 32, 40
- Border radius
  - Buttons / Cards: 12
  - BottomSheet / Large elements: 20
- Elevation / shadows
  - Card: elevation 2 / subtle shadow
  - Floating action / prominent: elevation 6
- Iconography
  - Konsisten antara MaterialIcons & custom pictograms untuk kategori layanan (simple line + filled on active)
- Accessibility
  - Kontras warna: pastikan teks pada Deep Blue/Green memenuhi WCAG AA jika berada di background putih.
  - Ukuran teks dapat di-scale; gunakan scalable text (MediaQuery.textScaleFactor).

2) Komponen desain (component library)
- AppBar (center logo atau title + action icons)
- Bottom Navigation (5 items: Beranda, Layanan, Pengaduan, Notifikasi, Profil)
  - Icon + label, active color = Deep Blue
- Cards
  - ServiceCard: icon circle + title + subtitle + chevron
  - ReportCard: id, title, status badge (colored), small timestamp
  - NewsCard: image thumbnail left, title, excerpt
- Primary CTA button
  - Full width, rounded, Deep Blue background, white text
- Secondary button / outline
  - White background, Deep Blue border, Deep Blue text
- Input fields
  - Rounded corners, subtle shadow, icon prefix when needed (search, phone, email)
- File/Media picker
  - Grid preview for images, camera quick-action
- Map view
  - Floating filter FAB, marker clustering, marker color by category/status
- Status timeline (tracking)
  - Vertical timeline with bullets and timestamps; current step highlighted (green)

3) Layout & wireframe per layar (ringkasan)
- Splash
  - Center logo + tagline + subtle loader; duration 1.2s → onboarding/login
- Onboarding (3 slides)
  - Full-bleed illustration/icon, headline, subtext, pagination dots, Skip / Next / Start
- Login / Auth
  - Logo, phone/email field, password, forgot link, primary MASUK, social login (Google), Sign up link
- Home (Beranda)
  - Welcome row (user avatar + greeting)
  - Search bar (sticky)
  - Prominent "Buat Pengaduan" banner CTA
  - Category horizontal scroll (chips)
  - Layanan Terdekat (vertical list)
  - News carousel / list
- Services (Layanan)
  - Search + filter, grid/list of ServiceCard, category header
- Create Report (Buat Pengaduan)
  - Multi-step form (prefer stepper bottom / paged)
    - Step 1: Kategori (radio/grid)
    - Step 2: Judul + Deskripsi (multiline)
    - Step 3: Lampiran (camera/gallery)
    - Step 4: Lokasi (use current location + drag marker on map)
    - Step 5: Review → Submit
  - Validation & helper hints
- Report Tracking (Pengaduan detail)
  - Header: report id + short title + status badge
  - Timeline vertical + last update
  - Photo gallery + location map + officer response / comment thread
- Map
  - Full screen map with cluster; bottom sheet for selected item details
- Notifications
  - Grouped sections (Today, Yesterday), swipe to dismiss / mark read
- Profile
  - Profile info + quick actions (My Reports, Address, Settings, Security)
- Admin Dashboard (web, separate)
  - Cards for metrics, table for recent reports, map, filters

4) Assets & export guide
- App icon (adaptive Android):
  - Provide source SVG & PNG exports: 48x48, 72x72, 96x96, 144x144, 192x192, 512x512
- Launcher & Splash:
  - 1242×2436 (iOS) not required for Android-only but provide scalable SVG so Flutter native_splash can use generated images/background color
- Illustrations:
  - Provide SVG for onboarding slides (outline-style with brand colors)
- Icons:
  - Provide as SVG + optimized PNG set; prefer vector for scaling
- Naming convention:
  - icons/ic_<name>.svg, images/ill_<name>.svg, logos/logo_full.svg
- Export formats: SVG (source), PNG 2x/3x if needed

5) Flutter implementation guidance (packages & architecture)
- State management: Provider / Riverpod / Bloc (choose one). Recommendation: Riverpod for testability + simplicity.
- Packages:
  - http / dio (API)
  - flutter_localizations (i18n)
  - google_maps_flutter (map)
  - geolocator (location)
  - image_picker / camera (media)
  - flutter_secure_storage (token)
  - firebase_messaging (push) OR local push plugin for notifications
  - cached_network_image
  - intl (dates/format)
- Folder structure: sesuai usulan Anda (good). Tambah:
  - lib/core/design_tokens.dart (colors/space)
  - lib/core/theme/app_theme.dart
  - lib/core/widgets/common/ (shared smaller widgets)
  - lib/features/reports/..., lib/features/services/...
- Accessibility & localization:
  - Base language: id (Indonesian). Support en later via intl ARB.
- Networking:
  - Use repository + service pattern, inject via Provider/Riverpod.

6) Flutter starter code (Theme + main scaffold)
Berikut starter minimal yang meng-encode design tokens dan scaffold bottom navigation. Saya sertakan dua file: lib/core/theme/app_theme.dart dan lib/main.dart — ini bisa Anda letakkan di repo sebagai starting point.

```dart name=lib/core/theme/app_theme.dart
import 'package:flutter/material.dart';

class AppColors {
  static const primary = Color(0xFF0B3D91); // Deep Blue
  static const primaryDark = Color(0xFF092F6F);
  static const success = Color(0xFF27AE60); // Green
  static const bg = Color(0xFFF5F7FA);
  static const surface = Color(0xFFFFFFFF);
  static const border = Color(0xFFE6E9EE);
  static const textPrimary = Color(0xFF0F1724);
  static const textSecondary = Color(0xFF6B7280);
}

class AppTheme {
  static ThemeData lightTheme() {
    final base = ThemeData.light();
    return base.copyWith(
      primaryColor: AppColors.primary,
      scaffoldBackgroundColor: AppColors.bg,
      colorScheme: base.colorScheme.copyWith(
        primary: AppColors.primary,
        secondary: AppColors.success,
      ),
      appBarTheme: const AppBarTheme(
        color: AppColors.surface,
        elevation: 0,
        iconTheme: IconThemeData(color: AppColors.textPrimary),
        titleTextStyle: TextStyle(
          color: AppColors.textPrimary,
          fontSize: 18,
          fontWeight: FontWeight.w600,
        ),
        centerTitle: true,
      ),
      textTheme: const TextTheme(
        headline1: TextStyle(fontSize: 28, fontWeight: FontWeight.w700, color: AppColors.textPrimary),
        headline2: TextStyle(fontSize: 22, fontWeight: FontWeight.w600, color: AppColors.textPrimary),
        bodyText1: TextStyle(fontSize: 16, fontWeight: FontWeight.w400, color: AppColors.textPrimary),
        bodyText2: TextStyle(fontSize: 14, color: AppColors.textSecondary),
      ),
      inputDecorationTheme: InputDecorationTheme(
        filled: true,
        fillColor: AppColors.surface,
        contentPadding: const EdgeInsets.symmetric(horizontal: 16, vertical: 12),
        border: OutlineInputBorder(
          borderRadius: BorderRadius.circular(12),
          borderSide: BorderSide(color: AppColors.border),
        ),
        enabledBorder: OutlineInputBorder(
          borderRadius: BorderRadius.circular(12),
          borderSide: BorderSide(color: AppColors.border),
        ),
      ),
      elevatedButtonTheme: ElevatedButtonThemeData(
        style: ElevatedButton.styleFrom(
          backgroundColor: AppColors.primary,
          foregroundColor: Colors.white,
          shape: RoundedRectangleBorder(borderRadius: BorderRadius.circular(12)),
          padding: const EdgeInsets.symmetric(vertical: 14, horizontal: 20),
          elevation: 2,
        ),
      ),
      bottomNavigationBarTheme: const BottomNavigationBarThemeData(
        backgroundColor: AppColors.surface,
        selectedItemColor: AppColors.primary,
        unselectedItemColor: AppColors.textSecondary,
        showUnselectedLabels: true,
        elevation: 8,
      ),
      cardTheme: const CardTheme(
        elevation: 2,
        color: AppColors.surface,
        shape: RoundedRectangleBorder(borderRadius: BorderRadius.all(Radius.circular(12))),
      ),
    );
  }
}
```

```dart name=lib/main.dart
import 'package:flutter/material.dart';
import 'core/theme/app_theme.dart';

void main() {
  runApp(const GlobZonApp());
}

class GlobZonApp extends StatelessWidget {
  const GlobZonApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'GlobZon',
      theme: AppTheme.lightTheme(),
      debugShowCheckedModeBanner: false,
      home: const MainScaffold(),
    );
  }
}

class MainScaffold extends StatefulWidget {
  const MainScaffold({super.key});
  @override
  State<MainScaffold> createState() => _MainScaffoldState();
}

class _MainScaffoldState extends State<MainScaffold> {
  int _currentIndex = 0;
  static const List<Widget> _pages = <Widget>[
    Center(child: Text('Beranda')),
    Center(child: Text('Layanan')),
    Center(child: Text('Pengaduan')),
    Center(child: Text('Notifikasi')),
    Center(child: Text('Profil')),
  ];

  void _onTap(int idx) => setState(() => _currentIndex = idx);

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: _pages[_currentIndex],
      bottomNavigationBar: BottomNavigationBar(
        currentIndex: _currentIndex,
        onTap: _onTap,
        items: const [
          BottomNavigationBarItem(icon: Icon(Icons.home), label: 'Beranda'),
          BottomNavigationBarItem(icon: Icon(Icons.widgets), label: 'Layanan'),
          BottomNavigationBarItem(icon: Icon(Icons.report), label: 'Pengaduan'),
          BottomNavigationBarItem(icon: Icon(Icons.notifications), label: 'Notifikasi'),
          BottomNavigationBarItem(icon: Icon(Icons.person), label: 'Profil'),
        ],
      ),
      floatingActionButton: FloatingActionButton.extended(
        onPressed: () {
          setState(() => _currentIndex = 2);
        },
        label: const Text('Buat Pengaduan'),
        icon: const Icon(Icons.add_alert),
        backgroundColor: AppTheme.lightTheme().primaryColor,
      ),
      floatingActionButtonLocation: FloatingActionButtonLocation.centerDocked,
    );
  }
}
```

7) Handoff & prototype: apa yang saya sarankan dibuat di Figma / desain
- Buat satu Figma file "GlobZon — Design System"
  - Page 1: Design tokens (colors, typography, spacing)
  - Page 2: Components (AppBar, Buttons, Inputs, Cards, Chips, Badges)
  - Page 3: Screens (Splash, Onboarding 1-3, Login, Home, Services, Create Report steps, Report detail, Map, Notifications, Profile)
  - Page 4: Admin Dashboard (web responsive)
- Eksport komponen sebagai library/asset; buat developer handoff (tokens + CSS/JSON export)
- Annotasi interaksi (taps, transitions, modal behavior, map marker interactions)

8) Roadmap implementasi (sprint-ready checklist)
- Milestone 1 — Design (1 week)
  - Membuat Figma mockups full screens (mobile portrait)
  - Review & iterate 1-2 kali
- Milestone 2 — Flutter scaffolding (2–3 days)
  - Inisialisasi project, theme, routing, bottom nav
  - Implement Splash, Onboarding, Login screens
- Milestone 3 — Core features (2–3 sprints)
  - Home, Services listing, Report create (form + media + location), Map integration
- Milestone 4 — Reports tracking & Notifications
- Milestone 5 — Profile, Settings, Admin API endpoints integration
- Non-functional: tests, analytics, CI, localization, accessibility review

9) Next steps saya bisa lakukan sekarang (pilih salah satu)
- A. Buat mockup Figma awal (I will produce a Figma file and share link) — saya butuh akses/here we agree on how to share.
- B. Buat artefak visual static (PNG/SVG) untuk semua layar berdasarkan blueprint — saya akan menghasilkan assets SVG untuk screens (but currently I can produce layout specs and code).
- C. Lanjutkan implementasi Flutter: saya buat PR scaffold awal dengan file di repo (but I need repo write confirmation and exact repo target branch/owner).
- D. Kirimkan berkas design tokens (.json / .yaml) + style guide lengkap.
