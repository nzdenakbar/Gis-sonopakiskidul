<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>SIG Sonopakis Kidul - KKN Ngestiharjo</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />
    <style>
        #map { height: 600px; }
        .custom-popup .leaflet-popup-content-wrapper {
            border-radius: 8px;
            box-shadow: 0 4px 12px rgba(0,0,0,0.15);
        }
        .custom-icon {
            filter: drop-shadow(1px 1px 3px rgba(0,0,0,0.3));
        }
        @media (max-width: 640px) {
            #map { height: 400px; }
        }
    </style>
</head>
<body class="bg-gray-50">
    <header class="bg-green-700 text-white py-6 px-4 shadow-md">
        <div class="container mx-auto">
            <div class="flex flex-col md:flex-row justify-between items-center">
                <div>
                    <h1 class="text-2xl md:text-3xl font-bold">SIG Sonopakis Kidul</h1>
                    <p class="text-green-100 mt-1">Sistem Informasi Geografis Bakti Masyarakat UPY</p>
                </div>
            </div>
        </div>
    </header>

    <main class="container mx-auto px-4 py-8">
        <div class="bg-white rounded-lg shadow-lg overflow-hidden">
            <div id="map" class="w-full"></div>
        </div>

        <div class="mt-8 grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
            <div class="bg-white p-6 rounded-lg shadow-md">
                <h2 class="text-xl font-semibold text-green-800 mb-4">Daftar RT</h2>
                <ul class="space-y-2" id="rt-list">
                    <!-- Daftar RT akan diisi oleh JavaScript -->
                </ul>
            </div>

            <div class="bg-white p-6 rounded-lg shadow-md">
                <h2 class="text-xl font-semibold text-green-800 mb-4">Fasilitas Publik</h2>
                <ul class="space-y-2" id="facilities-list">
                    <!-- Fasilitas publik akan diisi oleh JavaScript -->
                </ul>
            </div>

            <div class="bg-white p-6 rounded-lg shadow-md">
                <h2 class="text-xl font-semibold text-green-800 mb-4">Gambaran Lingkup Peta</h2>
                <p class="text-gray-700 mb-4">Klik pada marker untuk melihat informasi detail lokasi. Area yang diarsir menunjukkan batas wilayah Sonopakis Kidul.</p>
                <img src="peta.png" alt="Ilustrasi cara menggunakan peta interaktif dengan tangan mengklik marker pada smartphone" class="rounded-md" />
            </div>
        </div>
    </main>

    <footer class="bg-gray-800 text-white py-6 mt-8">
        <div class="container mx-auto px-4 text-center">
            <p>© 2025 Kelompok Bakti Masyarakat Upy - Sonopakis Kidul</p>
            <p class="text-gray-400 mt-2">Dikembangkan untuk mendukung sistem informasi wilayah</p>
        </div>
    </footer>

    <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
    <script>
        document.addEventListener('DOMContentLoaded', function() {
            // Koordinat pusat Sonopakis Kidul (contoh, harus disesuaikan)
            const centerLatLng = [-7.8088020, 110.3388182];
            
            // Inisialisasi peta dengan kontrol zoom
            const map = L.map('map', {
                zoomControl: false
            }).setView(centerLatLng, 15);
            
            // Tambahkan kontrol zoom dengan teks
            L.control.zoom({
                zoomInTitle: 'Perbesar',
                zoomOutTitle: 'Perkecil'
            }).addTo(map);
            
            // Tambahkan layer peta
            L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
                attribution: '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors'
            }).addTo(map);

            // Custom icon
            const houseIcon = L.icon({
                iconUrl: 'https://raw.githubusercontent.com/pointhi/leaflet-color-markers/master/img/marker-icon-green.png',
                iconSize: [25, 41],
                iconAnchor: [12, 41],
                popupAnchor: [1, -34],
                className: 'custom-icon'
            });

            const mosqueIcon = L.icon({
                iconUrl: 'https://raw.githubusercontent.com/pointhi/leaflet-color-markers/master/img/marker-icon-violet.png',
                iconSize: [25, 41],
                iconAnchor: [12, 41],
                popupAnchor: [1, -34],
                className: 'custom-icon'
            });

            const schoolIcon = L.icon({
                iconUrl: 'https://raw.githubusercontent.com/pointhi/leaflet-color-markers/master/img/marker-icon-gold.png',
                iconSize: [25, 41],
                iconAnchor: [12, 41],
                popupAnchor: [1, -34],
                className: 'custom-icon'
            });

            const fieldIcon = L.icon({
                iconUrl: 'https://raw.githubusercontent.com/pointhi/leaflet-color-markers/master/img/marker-icon-blue.png',
                iconSize: [25, 41],
                iconAnchor: [12, 41],
                popupAnchor: [1, -34],
                className: 'custom-icon'
            });

            // Data contoh - HARUS DISESUAIKAN DENGAN DATA LAPANGAN
            const locations = {
                rt: [
                    { id: 1, name: 'RT 01', lat: -7.8080766, lng: 110.3376309 },
                    { id: 2, name: 'RT 02', lat: -7.8073136, lng: 110.3370589 },
                    { id: 3, name: 'RT 03', lat: -7.8092162, lng: 110.3361259 },
                    { id: 4, name: 'RT 04', lat: -7.8098749, lng: 110.3374090 },
                    { id: 5, name: 'RT 05', lat: -7.8095205, lng: 110.3395896 },
                    { id: 6, name: 'RT 06', lat: -7.8093325, lng: 110.3399567 },
                    { id: 7, name: 'RT 07', lat: -7.8112265, lng: 110.3394213 },
                    { id: 8, name: 'RT 08', lat: -7.8083772, lng: 110.3418299 }
                ],
                facilities: [
                    { 
                      type: 'masjid', 
                        name: 'Masjid Tunas Muslim', 
                        lat: -7.8104801, 
                        lng: 110.3402860,
                        description: 'Masjid utama di Sonopakis Kidul. Fasilitas: tempat parkir luas, ruang sholat luas, dan kegiatan pengajian rutin.',
                        image: 'masjid tunas muslim.jpeg',
                        imageAlt: 'Masjid Al Ikhwan Sonopakis Kidul dengan arsitektur modern dan menara tinggi'
                    },
                    { 
                        type: 'masjid', 
                        name: 'Masjid Al Ikhwan', 
                        lat: -7.8097208, 
                        lng: 110.3375971,
                        description: 'Masjid utama di Sonopakis Kidul. Fasilitas: tempat parkir luas, ruang sholat luas, dan kegiatan pengajian rutin.',
                        image: 'masjid alikhwan.jpeg',
                        imageAlt: 'Masjid Al Ikhwan Sonopakis Kidul dengan arsitektur modern dan menara tinggi'
                    },
                    { 
                        type: 'masjid', 
                        name: 'Masjid Ar Roudhoh', 
                        lat: -7.8116058, 
                        lng: 110.3394927,
                        description: 'Masjid utama di Sonopakis Kidul. Fasilitas: tempat parkir luas, ruang sholat luas, dan kegiatan pengajian rutin.',
                        image: 'masjid ar roudhoh.jpeg',
                        imageAlt: 'Masjid Al Ikhwan Sonopakis Kidul dengan arsitektur modern dan menara tinggi'
                    },
                    { 
                        type: 'masjid', 
                        name: 'Masjid Babul Jannah', 
                        lat: -7.8072292, 
                        lng: 110.3384366,
                        description: 'Masjid utama di Sonopakis Kidul. Fasilitas: tempat parkir luas, ruang sholat luas, dan kegiatan pengajian rutin.',
                        image: 'masjidbabuljannah.jpeg',
                        imageAlt: 'Masjid Al Ikhwan Sonopakis Kidul dengan arsitektur modern dan menara tinggi'
                    },
                    { 
                        type: 'masjid', 
                        name: 'Musholla Rahmatul Jannah', 
                        lat: -7.8098483, 
                        lng: 110.3421203,
                        description: 'Masjid utama di Sonopakis Kidul. Fasilitas: tempat parkir luas, ruang sholat luas, dan kegiatan pengajian rutin.',
                        image: 'mushola.jpeg',
                        imageAlt: 'Masjid Al Ikhwan Sonopakis Kidul dengan arsitektur modern dan menara tinggi'
                    },
                    { 
                        type: 'sekolah', 
                        name: 'PAUD Bunga Matahari', 
                        lat: -7.8104967, 
                        lng: 110.3389,
                        description: 'Sekolah dasar di wilayah Sonopakis Kidul. Fasilitas: 6 ruang kelas, perpustakaan, dan lapangan olahraga.',
                        image: 'paud.jpeg',
                        imageAlt: 'Gedung PAUD Sonopakis Kidul dengan halaman bermain dan taman sekolah yang asri'
                    },
                    { 
                        type: 'sekolah', 
                        name: 'TK Pertiwi 41', 
                        lat: -7.8117703, 
                        lng: 110.3378210,
                        description: 'Sekolah dasar di wilayah Sonopakis Kidul. Fasilitas: 6 ruang kelas, perpustakaan, dan lapangan olahraga.',
                        image: 'tk pertiwi.jpeg',
                        imageAlt: 'Gedung TK Sonopakis Kidul dengan halaman bermain dan taman sekolah yang asri'
                    },
                     { 
                        type: 'sekolah', 
                        name: 'SD Sonosewu', 
                        lat: -7.8120486, 
                        lng: 110.3377308,
                        description: 'Sekolah dasar di wilayah Sonopakis Kidul. Fasilitas: 6 ruang kelas, perpustakaan, dan lapangan olahraga.',
                        image: 'sd sonosewu.jpeg',
                        imageAlt: 'Gedung SD Sonopakis dengan halaman bermain dan taman sekolah yang asri'
                    },
                     { 
                        type: 'dukuh', 
                        name: 'Rumah Dukuh', 
                        lat: -7.8097148, 
                        lng: 110.3421002,
                        description: 'Kediaman kepala dukuh Sonopakis Kidul. Hubungi untuk keperluan administrasi warga.',
                        image: 'rumahdukuh.jpeg',
                        imageAlt: 'Rumah dukuh Sonopakis Kidul'
                    },
                     { 
                        type: 'dukuh', 
                        name: 'Balai Karang Taruna', 
                        lat: -7.8105565, 
                        lng: 110.3389690,
                        description: 'Balai Karang Taruna Sonopakis Kidul.',
                        image: 'balaipemuda.jpeg',
                        imageAlt: 'Balai Karang Taruna Sonopakis Kidul dengan teras yang luas dan taman depan yang rapi'
                    },
                    { 
                        type: 'lapangan', 
                        name: 'Lapangan Volly Sanggrahan', 
                        lat: -7.8099600, 
                        lng: 110.3415094,
                        description: 'Lapangan olahraga untuk kegiatan masyarakat, seperti sepak bola dan voli.',
                        image: 'lapangansanggrahan.jpeg',
                        imageAlt: 'Lapangan olahraga di Sonopakis Kidul'
                    },
                    { 
                        type: 'lapangan', 
                        name: 'Lapangan Volly Soboman', 
                        lat: -7.8112341, 
                        lng: 110.3392728,
                        description: 'Lapangan olahraga untuk kegiatan masyarakat, seperti sepak bola dan voli.',
                        image: 'lapangan soboman.jpeg',
                        imageAlt: 'Lapangan olahraga di Sonopakis Kidul'
                    },
                    { 
                        type: 'lapangan', 
                        name: 'Lapangan Volly Sonopakis Kidul', 
                        lat: -7.8083323,
                        lng: 110.3358771,
                        description: 'Lapangan olahraga untuk kegiatan masyarakat, seperti sepak bola dan voli.',
                        image: 'lapangan sonopakis kidul.jpeg',
                        imageAlt: 'Lapangan olahraga di Sonopakis Kidul'
                    },
                ]
            };

            // Koordinat batas wilayah Sonopakis Kidul (perkiraan berdasarkan lokasi RT dan fasilitas)
            const boundaryCoordinates = [
                [-7.8065, 110.3350], // Titik barat laut
                [-7.8060, 110.3380], // Titik utara
                [-7.8065, 110.3430], // Titik timur laut
                [-7.8105, 110.3435], // Titik timur
                [-7.8125, 110.3420], // Titik tenggara
                [-7.8125, 110.3370], // Titik selatan
                [-7.8115, 110.3350], // Titik barat daya
                [-7.8095, 110.3345], // Titik barat
                [-7.8065, 110.3350]  // Kembali ke titik awal
            ];

            // Tambahkan polygon batas wilayah
            const boundaryPolygon = L.polygon(boundaryCoordinates, {
                color: '#059669',      // Warna garis border (green-600)
                weight: 3,             // Ketebalan garis
                opacity: 0.8,          // Transparansi garis
                fillColor: '#10b981',  // Warna isian (green-500)
                fillOpacity: 0.1       // Transparansi isian
            }).addTo(map);

            // Tambahkan popup untuk batas wilayah
            boundaryPolygon.bindPopup(`
                <div class="text-center">
                    <h3 class="font-bold text-lg text-green-800">Batas Wilayah</h3>
                    <p class="text-gray-700">Sonopakis Kidul</p>
                    <p class="text-sm text-gray-600 mt-2">Dukuh Ngestiharjo, Bantul</p>
                    <p class="text-xs text-gray-500 mt-1">Klik pada marker untuk informasi detail fasilitas</p>
                </div>
            `);

            // Fungsi untuk membuat popup content
            function createPopupContent(location) {
                let content = `
                    <div class="max-w-xs">
                        <h3 class="text-lg font-bold mb-2 text-green-800">${location.name}</h3>
                        <div class="mb-3">
                            <img src="${location.image}" alt="${location.imageAlt}" class="w-full rounded-md mb-1" />
                            <p class="text-xs text-gray-500">${location.imageAlt}</p>
                        </div>
                        <p class="text-gray-700 mb-3">${location.description}</p>
                        <div class="flex justify-between items-center">
                            <a href="https://www.google.com/maps/dir/?api=1&destination=${location.lat},${location.lng}" 
                               target="_blank" 
                               class="bg-green-600 hover:bg-green-700 text-white px-3 py-1 rounded text-sm transition">
                                Petunjuk Arah
                            </a>
                            <span class="text-xs text-gray-500">
                                Lat: ${location.lat.toFixed(4)}, Lng: ${location.lng.toFixed(4)}
                            </span>
                        </div>
                    </div>
                `;
                return content;
            }

            // Tambahkan marker untuk RT
            locations.rt.forEach(rt => {
                const marker = L.marker([rt.lat, rt.lng], { icon: houseIcon })
                    .addTo(map)
                    .bindTooltip(`RT ${rt.id}`, {permanent: true, direction: 'right'})
                    .bindPopup(`<div class="text-center">
                        <h3 class="font-bold text-lg">RT ${rt.id}</h3>
                        <p class="text-gray-700">${rt.name}</p>
                        <div class="mt-2 text-sm text-gray-500">
                            <p>Wilayah RT ${rt.id}</p>
                            <p>Sonopakis Kidul, Ngestiharjo</p>
                        </div>
                    </div>`);
            });

            // Tambahkan marker untuk fasilitas
            locations.facilities.forEach(facility => {
                let icon;
                if (facility.type === 'masjid') icon = mosqueIcon;
                else if (facility.type === 'sekolah') icon = schoolIcon;
                else if (facility.type === 'lapangan') icon = fieldIcon; // Use field icon for the field
                else icon = houseIcon;

                const marker = L.marker([facility.lat, facility.lng], { icon })
                    .addTo(map)
                    .bindTooltip(facility.name, {permanent: true, direction: 'right'})
                    .bindPopup(createPopupContent(facility), {
                        className: 'custom-popup',
                        maxWidth: 300
                    });
            });

            // Isi daftar RT
            const rtList = document.getElementById('rt-list');
            locations.rt.forEach(rt => {
                const li = document.createElement('li');
                li.className = 'flex items-center';
                li.innerHTML = `
                    <div class="flex items-center justify-between w-full">
                        <div class="flex items-center">
                            <span class="w-8 h-8 bg-green-100 text-green-800 flex items-center justify-center rounded-full mr-3 font-medium">${rt.id}</span>
                            <span><b>RT ${rt.id}:</b> ${rt.name} Sonopakis Kidul</span>
                        </div>
                        <a href="https://www.google.com/maps/dir/?api=1&destination=${rt.lat},${rt.lng}" 
                           target="_blank"
                           class="ml-2 bg-blue-500 hover:bg-blue-600 text-white px-2 py-1 rounded text-sm">
                            Petunjuk Arah
                        </a>
                    </div>
                `;
                rtList.appendChild(li);
            });

            // Isi daftar fasilitas
            const facilitiesList = document.getElementById('facilities-list');
            locations.facilities.forEach(facility => {
                const li = document.createElement('li');
                li.className = 'flex items-center';
                let icon = '';
                
                if (facility.type === 'masjid') {
                    icon = 'text-purple-600';
                } else if (facility.type === 'sekolah') {
                    icon = 'text-yellow-600';
                } else if (facility.type === 'lapangan') {
                    icon = 'text-blue-600'; // Different color for field
                } else {
                    icon = 'text-blue-600';
                }

                li.innerHTML = `
                    <div class="flex items-center justify-between w-full">
                        <div class="flex items-center">
                            <svg class="w-6 h-6 ${icon} mr-3" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" 
                                ${
                                    facility.type === 'masjid' ? 
                                    'd="M3 12l2-2m0 0l7-7 7 7M5 10v10a1 1 0 001 1h3m10-11l2 2m-2-2v10a1 1 0 01-1 1h-3m-6 0a1 1 0 001-1v-4a1 1 0 011-1h2a1 1 0 011 1v4a1 1 0 001 1m-6 0h6"' : 
                                    (facility.type === 'sekolah' ? 
                                    'd="M12 6.253v13m0-13C10.832 5.477 9.246 5 7.5 5S4.168 5.477 3 6.253v13C4.168 18.477 5.754 18 7.5 18s3.332.477 4.5 1.253m0-13C13.168 5.477 14.754 5 16.5 5c1.747 0 3.332.477 4.5 1.253v13C19.832 18.477 18.247 18 16.5 18c-1.746 0-3.332.477-4.5 1.253"' : 
                                    'd="M17.657 16.657L13.414 20.9a1.998 1.998 0 01-2.827 0l-4.244-4.243a8 8 0 1111.314 0z M15 11a3 3 0 11-6 0 3 3 0 016 0z"')
                                }></path>
                            </svg>
                            <span>${facility.name} ${facility.type === 'masjid' ? '(Masjid)' : facility.type === 'sekolah' ? '(Sekolah)' : '(Lapangan)'}</span>
                        </div>
                        <a href="https://www.google.com/maps/dir/?api=1&destination=${facility.lat},${facility.lng}" 
                           target="_blank"
                           class="ml-2 bg-blue-500 hover:bg-blue-600 text-white px-2 py-1 rounded text-sm">
                            Petunjuk Arah
                        </a>
                    </div>
                `;
                facilitiesList.appendChild(li);
            });

            // Fungsi untuk memperbesar peta ke seluruh area dengan batas
            function zoomToAllLocations() {
                const allCoords = [];
                
                // Tambahkan koordinat RT
                locations.rt.forEach(rt => allCoords.push([rt.lat, rt.lng]));
                
                // Tambahkan koordinat fasilitas
                locations.facilities.forEach(fac => allCoords.push([fac.lat, fac.lng]));
                
                // Tambahkan koordinat batas wilayah
                boundaryCoordinates.forEach(coord => allCoords.push(coord));
                
                const bounds = new L.LatLngBounds(allCoords);
                map.fitBounds(bounds, { padding: [20, 20] });
            }

            // Zoom ke seluruh area saat halaman dimuat
            setTimeout(zoomToAllLocations, 1000);
        });
    </script>
</body>
</html>
