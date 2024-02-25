# Movie-Katalok-Testing

Deploy https://muvieslove.netlify.app/
Lathihan Membuat Movie Katalog

Mari kita uji coba tahap pertama pada skenario "Menyukai Film", yaitu “Widget untuk menyukai film ditampilkan”. Buat berkas baru bernama likeMovie.test.js pada folder ”tests”.

Karena ingin menguji objek LikeButtonInitiator, kita perlu mengimpornya terlebih dahulu seperti berikut.

Silakan tuliskan kode berikut pada berkas likeMovie.test.js.

likeMovie.test.js

    import LikeButtonInitiator from '../src/scripts/utils/like-button-initiator';
        
        describe('Liking A Movie', () => {
        it('should show the like button when the movie has not been liked before', async () => {
        
        });
    });


Untuk menguji ini, kita butuh sebuah elemen <div id="likeButtonContainer"></div>. Elemen inilah yang dijadikan parameter pada method init milik LikeButtonContainer. Jika Anda lupa, perhatikan kode bercetak tebal berikut.

detail.js

    const Detail = {
    async render() {
        return `
        <div id="movie" class="movie"></div>
        <div id="likeButtonContainer"></div>
        `;
    },


kita tinggal menambahkannya ke document.body. Silakan sesuaikan kode tesnya menjadi seperti berikut.

likeMovie.test.js

    document.body.innerHTML = '<div id="likeButtonContainer"></div>';


LikeButtonInitiator. Sebagaimana kode yang ada pada detail.js, kita perlu memberikan elemen dan data film yang dibutuhkan pada method init.

likeMovie.test.js

    await LikeButtonInitiator.init({
        likeButtonContainer: document.querySelector('#likeButtonContainer'),
        movie: {
            id: 1,
        },
    });


Kita bisa melihat implementasi dari LikeButtonInitiator. Objek tersebut akan menjalankan createLikeButtonTemplate bila film belum disukai. Bila film sudah disukai, ia akan menjalankan createLikedButtonTemplate.

Perbedaan template yang dihasilkan oleh dua method di atas ada pada atribut aria-label

Jadi, kita perlu memeriksa aria-label mana yang muncul pada tes.


hasil 

    PASS  tests/likeMovie.test.js
    Liking A Movie
        ✓ should show the like button when the movie has not been liked before (19 ms)
    
    Test Suites: 1 passed, 1 total
    Tests:       1 passed, 1 total
    Snapshots:   0 total
    Time:        0.741 s, estimated 1 s
    Ran all test suites.

Untuk tes berikutnya, kita perlu memastikan bahwa widget unlike tidak ditampilkan. Ini untuk memastikan saja.


Untuk tes berikutnya, kita perlu memastikan bahwa widget unlike tidak ditampilkan. Ini untuk memastikan saja.


    it('should not show the unlike button when the movie has not been liked before', async () => {
        document.body.innerHTML = '<div id="likeButtonContainer"></div>';
        await LikeButtonInitiator.init({
        likeButtonContainer: document.querySelector('#likeButtonContainer'),
        movie: {
            id: 1,
        },
        });
        expect(document.querySelector('[aria-label="unlike this movie"]')).toBeFalsy();
    });


## Menyukai Film.

    1. Film belum disukai. OK
    2. Widget untuk menyukai film ditampilkan. OK


Sebelum kita lanjut ke tahap berikutnya, ada dua metode yang perlu diubah, yaitu createLikeButtonTemplate dan createLikedButtonTemplate. Kedua metode ini terlalu mirip. Perbedaan hanya ada pada huruf “d”.

*TODO:*

    1. Memberi nama yang lebih berbeda untuk createLikeButtonTemplate dan createLikedButtonTemplate


    it('should be able to like the movie', async () => {
        document.body.innerHTML = '<div id="likeButtonContainer"></div>';
        await LikeButtonInitiator.init({
        likeButtonContainer: document.querySelector('#likeButtonContainer'),
        movie: {
            id: 1,
        },
        });
    });

Untuk mengerjakan tes di atas, ada beberapa hal yang kita perlu lakukan.

    1. Menyimulasikan widget ditekan.
    2. Memeriksa jika film memang disimpan dengan benar atau tidak.

Mari kita lakukan hal yang pertama. Untuk menyimulasikan widget, kita bisa menggunakan dispatchEvent.

    document.querySelector('#likeButton').dispatchEvent(new Event('click'));

Setelah membangkitkan event click, asumsinya, film tersimpan di FavoriteMovieIdb. Jadi, kita perlu memiliki akses ke FavoriteMovieIdb dalam tes ini.

    import FavoriteMovieIdb from '../src/scripts/data/favorite-movie-idb';


Untuk menguji filmnya tersimpan atau tidak, kita dapat memanfaatkan getMovie seperti contoh berikut.


  // Memastikan film berhasil disukai
    const movie = await FavoriteMovieIdb.getMovie(1);
    expect(movie).toEqual({ id: 1 });


Jalankan Test


    IYAWAN@DESKTOP-1RD67NU MINGW64 /d/MSIB/PROJECT/movie-catalogue-jest-configured (main)
    $ npm test

    > movie-catalogue@1.0.0 test
    > jest

    PASS  tests/likeMovie.test.js (11.077 s)

    RUNS  tests/sampleTest.test.js
    D:\MSIB\PROJECT\movie-catalogue-jest-configured\src\scripts\utils\like-button-initiator.js:101
        var likeButton = document.querySelector('#likeButton');
                                ^

    TypeError: Cannot read properties of undefined (reading 'querySelector')
        at Object._renderLiked (D:\MSIB\PROJECT\movie-catalogue-jest-configured\src\scripts\utils\like-button-initiator.js:101:31)
        at _callee2$ (D:\MSIB\PROJECT\movie-catalogue-jest-configured\src\scripts\utils\like-button-initiator.js:49:20)
        at tryCatch (D:\MSIB\PROJECT\movie-catalogue-jest-configured\src\scripts\utils\like-button-initiator.js:11:1062)
        at Generator.<anonymous> (D:\MSIB\PROJECT\movie-catalogue-jest-configured\src\scripts\utils\like-button-initiator.js:11:3012)
        at Generator.next (D:\MSIB\PROJECT\movie-catalogue-jest-configured\src\scripts\utils\like-button-initiator.js:11:1699)
        at asyncGeneratorStep (D:\MSIB\PROJECT\movie-catalogue-jest-configured\src\scripts\utils\like-button-initiator.js:12:103)
        at _next (D:\MSIB\PROJECT\movie-catalogue-jest-configured\src\scripts\utils\like-button-initiator.js:13:194)
        at runNextTicks (node:internal/process/task_queues:60:5)
        at processImmediate (node:internal/timers:447:9)

    Node.js v18.17.1

Bisa jadi Anda peroleh jumlah dan urutan error yang berbeda. Hal ini karena Jest menjalankan tes dengan urutan yang acak. Namun, hal yang pasti adalah tes ketiga membuat tes lainnya error.

Apa sebab kegagalan di atas? Kita menambahkan film ber-id 1 ke FavoriteMovieIdb. Sebab tesnya dijalankan secara acak, ada kemungkinan tes ketiga dijalankan sebelum tes pertama dan kedua. Alhasil, ketika tes pertama dan kedua dijalankan, film dengan ID 1 sudah ada dalam FavoriteMovieIdb. Keberadaan film ini dalam FavoriteMovieIdb menyebabkan tes pertama dan kedua gagal. Kedua tes tersebut mengasumsikan film dengan ID 1 belum disukai.

Cara untuk mengatasi masalah di atas sebenarnya gampang. Kita hapus saja film ID 1 dari FavoriteMovieIdb menggunakan FavoriteMovieIdb.deleteMovie(1). Namun, sebelum menerapkan solusi tersebut, ada yang perlu kita bicarakan terlebih dahulu terkait jenis-jenis testing.

Di dunia testing otomatis, ada beberapa level testing.


    Unit Testing
    Integration Testing
    End-to-End Testing


Silakan sesuaikan kode seperti berikut.

    await FavoriteMovieIdb.deleteMovie(1);


kita buat abstraksinya. Buatlah sebuah function addLikeButtonContainer seperti berikut.


    const addLikeButtonContainer = () => {
        document.body.innerHTML = '<div id="likeButtonContainer"></div>';
    };
    beforeEach(() => {
        addLikeButtonContainer();
    });


Maka keseluruan code LikeMovie.text.js

    import LikeButtonInitiator from '../src/scripts/utils/like-button-initiator';
    import FavoriteMovieIdb from '../src/scripts/data/favorite-movie-idb';
    
    describe('Liking A Movie', () => {
    const addLikeButtonContainer = () => {
        document.body.innerHTML = '<div id="likeButtonContainer"></div>';
    };
    
    beforeEach(() => {
        addLikeButtonContainer();
    });
    
    it('should show the like button when the movie has not been liked before', async () => {
        await LikeButtonInitiator.init({
        likeButtonContainer: document.querySelector('#likeButtonContainer'),
        movie: {
            id: 1,
        },
        });
    
        expect(document.querySelector('[aria-label="like this movie"]')).toBeTruthy();
    });
    
    it('should not show the unlike button when the movie has not been liked before', async () => {
        await LikeButtonInitiator.init({
        likeButtonContainer: document.querySelector('#likeButtonContainer'),
        movie: {
            id: 1,
        },
        });
    
        expect(document.querySelector('[aria-label="unlike this movie"]')).toBeFalsy();
    });
    
    it('should be able to like the movie', async () => {
        await LikeButtonInitiator.init({
        likeButtonContainer: document.querySelector('#likeButtonContainer'),
        movie: {
            id: 1,
        },
        });
    
        document.querySelector('#likeButton').dispatchEvent(new Event('click'));
    
        // Memastikan film berhasil disukai
        const movie = await FavoriteMovieIdb.getMovie(1);
        expect(movie).toEqual({ id: 1 });
    
        await FavoriteMovieIdb.deleteMovie(1);
    });
    });


## Menyukai Film.


    1. Film belum disukai. OK
    2. Widget untuk menyukai film ditampilkan. OK
    3. Widget menyukai film ditekan oleh pengguna. OK
    4. Film ditambahkan ke daftar film yang disukai.

        a. Film berhasil ditambahkan. OK
        b. Ternyata film sudah disukai


Sekarang kita bisa lanjut menguji skenario negatif, yaitu "Ternyata film sudah disukai". Kita siapkan method tesnya.

    it('should not add a movie again when its already liked', async () => {
        await LikeButtonInitiator.init({
        likeButtonContainer: document.querySelector('#likeButtonContainer'),
        movie: {
            id: 1,
        },
        });
        // Tambahkan film dengan ID 1 ke daftar film yang disukai
        await FavoriteMovieIdb.putMovie({ id: 1 });
        // Simulasikan pengguna menekan tombol suka film
        document.querySelector('#likeButton').dispatchEvent(new Event('click'));

            
        // Tidak ada film yang ganda
        expect(await FavoriteMovieIdb.getAllMovies()).toEqual([{ id: 1 }]);
        await FavoriteMovieIdb.deleteMovie(1);
    });


Sesuai dengan spesifikasi yang telah dibuat, bila film sudah berada dalam daftar film yang disukai, kita tidak perlu menambahkannya kembali. Untuk memeriksa ini, kita bisa menggunakan method getAllMovies yang telah disediakan oleh FavoriteMovieIdb.


## Menyukai Film.


    1. Film belum disukai. OK
    2. Widget untuk menyukai film ditampilkan. OK
    3. Widget menyukai film ditekan oleh pengguna. OK
    4. Film ditambahkan ke daftar film yang disukai. OK

        a. Film berhasil ditambahkan. OK
        b. Ternyata film sudah disukai OK
        c. Ternyata film sudah disukai. OK
            i. Tidak perlu menyimpan kembali. OK
        c. Data film tidak memiliki ID.
            i. Sistem tidak memproses penyimpanan.
            ii. Sistem tidak gagal



Saatnya menguji kebutuhan di atas.

 
    it('should not add a movie when it has no id', async () => {
        await LikeButtonInitiator.init({
        likeButtonContainer: document.querySelector('#likeButtonContainer'),
        movie: {},
        });
        document.querySelector('#likeButton').dispatchEvent(new Event('click'));
        expect(await FavoriteMovieIdb.getAllMovies()).toEqual([]);
    });

Pada testing di atas, perhatikan nilai movie yang kita berikan ke LikeButtonInitiator. Nilai tersebut tidak memiliki ID.

        npm test

        > movie-catalogue@1.0.0 test
        > jest

        FAIL  tests/likeMovie.test.js (5.98 s)
        ● Liking A Movie › should not add a movie when it has no id

            DataError: Data provided to an operation does not meet requirements.

            12 | const FavoriteMovieIdb = {
            13 |   async getMovie(id) {
            > 14 |     return (await dbPromise).get(OBJECT_STORE_NAME, id);
                |                              ^
            15 |   },
            16 |   async getAllMovies() {
            17 |     return (await dbPromise).getAll(OBJECT_STORE_NAME);

            at valueToKey (node_modules/fake-indexeddb/build/cjs/lib/valueToKey.js:58:11)
            at get (src/scripts/data/favorite-movie-idb.js:14:30)
            at call (src/scripts/data/favorite-movie-idb.js:2:1)
            at Generator.tryCatch (src/scripts/data/favorite-movie-idb.js:2:1)
            at Generator._invoke [as next] (src/scripts/data/favorite-movie-idb.js:2:1)
            at asyncGeneratorStep (src/scripts/data/favorite-movie-idb.js:2:1)
            at asyncGeneratorStep (src/scripts/data/favorite-movie-idb.js:2:1)

        PASS  tests/sampleTest.test.js

        Test Suites: 1 failed, 1 passed, 2 total
        Tests:       1 failed, 5 passed, 6 total
        Snapshots:   0 total
        Time:        10.854 s
        Ran all test suites.

        DIYAWAN@DESKTOP-1RD67NU MINGW64 /d/MSIB/PROJECT/movie-catalogue-jest-configured (main)
        $

    
kita bisa menonaktifkan tes di atas dengan mengubah metode it menjadi xit.

    PASS  tests/likeMovie.test.js (11.005 s)
    PASS  tests/sampleTest.test.js


## Menyukai Film.

    1. Film belum disukai. OK
    2. Widget untuk menyukai film ditampilkan. OK
    3. Widget menyukai film ditekan oleh pengguna. OK
    4. Film ditambahkan ke daftar film yang disukai. OK

        a. Film berhasil ditambahkan. OK
        b. Ternyata film sudah disukai OK
        c. Ternyata film sudah disukai. OK
            i. Tidak perlu menyimpan kembali. OK
        c. Data film tidak memiliki ID.
            i. Sistem tidak memproses penyimpanan.
            ii. Sistem tidak gagal

## Batal Menyukai Film. 
    1. Film sudah disukai.
    2. Widget untuk batal menyukai film ditampilkan.
    3. Widget pembatalan ditekan oleh pengguna.
    4. Film dihapus dari daftar film yang disukai.
        a. Film berhasil dihapus.
        b. Ternyata film tidak ada dalam daftar film yang disukai.


unlikeMovie.test.js

    import LikeButtonInitiator from '../src/scripts/utils/like-button-initiator';
    import FavoriteMovieIdb from '../src/scripts/data/favorite-movie-idb';
    
    describe('Unliking A Movie', () => {
    const addLikeButtonContainer = () => {
        document.body.innerHTML = '<div id="likeButtonContainer"></div>';
    };
    
    beforeEach(async () => {
        addLikeButtonContainer();
        await FavoriteMovieIdb.putMovie({ id: 1 });
    });
    
    afterEach(async () => {
        await FavoriteMovieIdb.deleteMovie(1);
    });
    
    it('should display unlike widget when the movie has been liked', async () => {
        await LikeButtonInitiator.init({
        likeButtonContainer: document.querySelector('#likeButtonContainer'),
        movie: {
            id: 1,
        },
        });
    
        expect(document.querySelector('[aria-label="unlike this movie"]')).toBeTruthy();
    });
    
    it('should not display like widget when the movie has been liked', async () => {
        await LikeButtonInitiator.init({
        likeButtonContainer: document.querySelector('#likeButtonContainer'),
        movie: {
            id: 1,
        },
        });
    
        expect(document.querySelector('[aria-label="like this movie"]')).toBeFalsy();
    });
    });

Pada kode di atas, kita menambahkan movie ID 1 pada daftar film yang disukai sebelum tiap test case dijalankan. Hal ini bisa dilihat pada metode beforeEach. Setelah tiap metode tes dijalankan, kita hapus movie ID 1 dari daftar tersebut. Hal ini bisa dilihat pada metode afterEach.

Untuk menguji film dihapus dari daftar film disukai ketika widget-nya ditekan, kita simulasikan user menekan tombol tersebut dengan cara membangkitkan event click.


    it('should be able to remove liked movie from the list', async () => {
        await LikeButtonInitiator.init({
        likeButtonContainer: document.querySelector('#likeButtonContainer'),
        movie: {
            id: 1,
        },
        });
        document.querySelector('[aria-label="unlike this movie"]').dispatchEvent(new Event('click'));
        expect(await FavoriteMovieIdb.getAllMovies()).toEqual([]);
    });


 terakhir adalah membatalkan film yang disukai ketika tidak ada di dalam daftar

    it('should not throw error when user click unlike widget if the unliked movie is not in the list', async () => {
        await LikeButtonInitiator.init({
        likeButtonContainer: document.querySelector('#likeButtonContainer'),
        movie: {
            id: 1,
        },
        });
        // Hapus dulu film dari daftar film yang disukai
        await FavoriteMovieIdb.deleteMovie(1);
        // Kemudian, simulasikan pengguna menekan widget batal menyukai film
        document.querySelector('[aria-label="unlike this movie"]').dispatchEvent(new Event('click'));
        expect(await FavoriteMovieIdb.getAllMovies()).toEqual([]);
    });


### TODO:

    1. Memberi nama yang lebih berbeda untuk createLikeButtonTemplate dan createLikedButtonTemplate.

    2. Alur negatif: sistem tidak memproses penyimpanan dan menyebabkan kegagalan jika menyimpan movie tanpa ID.

    ## Menyukai Film.

    1. Film belum disukai. OK
    2. Widget untuk menyukai film ditampilkan. OK
    3. Widget menyukai film ditekan oleh pengguna. OK
    4. Film ditambahkan ke daftar film yang disukai. OK

        a. Film berhasil ditambahkan. OK
        b. Ternyata film sudah disukai OK
        c. Ternyata film sudah disukai. OK
            i. Tidak perlu menyimpan kembali. OK
        c. Data film tidak memiliki ID.
            i. Sistem tidak memproses penyimpanan.
            ii. Sistem tidak gagal

## Batal Menyukai Film. OK

    1. Film sudah disukai.  OK
    2. Widget untuk batal menyukai film ditampilkan. OK
    3. Widget pembatalan ditekan oleh pengguna. OK
    4. Film dihapus dari daftar film yang disukai. OK
        a. Film berhasil dihapus. OK
        b. Ternyata film tidak ada dalam daftar film yang disukai. OK

## TODO:

    1. Memberi nama yang lebih berbeda untuk createLikeButtonTemplate dan createLikedButtonTemplate.
    2. Alur negatif: sistem tidak memproses penyimpanan dan menyebabkan kegagalan jika menyimpan movie tanpa ID.


### Katakanlah “No Crash” pada Aplikasi

Pertama, buka kembali berkas tes likeMovie.test.js. Perhatikan test case berikut.

Kita mendefinisikan test case dengan method xit. Masih ingat dengan fungsi dari method itu, kan? Nah, mari kita aktifkan lagi test case ini dengan cara mengubah xit menjadi it.


    it('should not add a movie when it has no id', async () => {
        await LikeButtonInitiator.init({
        likeButtonContainer: document.querySelector('#likeButtonContainer'),
        movie: {},
        });
    
        document.querySelector('#likeButton').dispatchEvent(new Event('click'));
    
        expect(await FavoriteMovieIdb.getAllMovies()).toEqual([]);
    });


Setelah memperoleh tes yang gagal, kita dapat mengubah method putMovie (make it run) menjadi seperti berikut.

        npm test

        > movie-catalogue@1.0.0 test
        > jest

        PASS  tests/unlikeMovie.test.js
        FAIL  tests/likeMovie.test.js
        ● Liking A Movie › should not add a movie when it has no id

            DataError: Data provided to an operation does not meet requirements.

            12 | const FavoriteMovieIdb = {
            13 |   async getMovie(id) {
            > 14 |     return (await dbPromise).get(OBJECT_STORE_NAME, id);
                |                              ^
            15 |   },
            16 |   async getAllMovies() {
            17 |     return (await dbPromise).getAll(OBJECT_STORE_NAME);

            at valueToKey (node_modules/fake-indexeddb/build/cjs/lib/valueToKey.js:58:11)
            at get (src/scripts/data/favorite-movie-idb.js:14:30)
            at call (src/scripts/data/favorite-movie-idb.js:2:1)
            at Generator.tryCatch (src/scripts/data/favorite-movie-idb.js:2:1)
            at Generator._invoke [as next] (src/scripts/data/favorite-movie-idb.js:2:1)
            at asyncGeneratorStep (src/scripts/data/favorite-movie-idb.js:2:1)
            at asyncGeneratorStep (src/scripts/data/favorite-movie-idb.js:2:1)

        PASS  tests/sampleTest.test.js

        Test Suites: 1 failed, 2 passed, 3 total 
        Tests:       1 failed, 9 passed, 10 total
        Snapshots:   0 total
        Time:        9.3 s
        Ran all test suites.

        DIYAWAN@DESKTOP-1RD67NU MINGW64 /d/MSIB/PROJECT/movie-catalogue-jest-configured (main)
        $

ubah favorite-movie-idb.js
    
        
    const FavoriteMovieIdb = {
    async getMovie(id) {
        if (!id) {
        return;
        }
        // eslint-disable-next-line consistent-return
        return (await dbPromise).get(OBJECT_STORE_NAME, id);
    },
    async getAllMovies() {
        return (await dbPromise).getAll(OBJECT_STORE_NAME);
    },
    async putMovie(movie) {
        // eslint-disable-next-line no-prototype-builtins
        if (!movie.hasOwnProperty('id')) {
        return;
        }
        // eslint-disable-next-line consistent-return
        return (await dbPromise).put(OBJECT_STORE_NAME, movie);
    },
    async deleteMovie(id) {
        return (await dbPromise).delete(OBJECT_STORE_NAME, id);
    },
    };

    export default FavoriteMovieIdb;



## Menyukai Film.

    1. Film belum disukai. OK
    2. Widget untuk menyukai film ditampilkan. OK
    3. Widget menyukai film ditekan oleh pengguna. OK
    4. Film ditambahkan ke daftar film yang disukai. OK

        a. Film berhasil ditambahkan. OK
        b. Ternyata film sudah disukai OK
        c. Ternyata film sudah disukai. OK
            i. Tidak perlu menyimpan kembali. OK
        c. Data film tidak memiliki ID. OK
            i. Sistem tidak memproses penyimpanan. OK
            ii. Sistem tidak gagal OK

## Batal Menyukai Film. OK

    1. Film sudah disukai.  OK
    2. Widget untuk batal menyukai film ditampilkan. OK
    3. Widget pembatalan ditekan oleh pengguna. OK
    4. Film dihapus dari daftar film yang disukai. OK
        a. Film berhasil dihapus. OK
        b. Ternyata film tidak ada dalam daftar film yang disukai. OK

## Jagalah Kebersihan

## TODO

    1. Memberi nama yang lebih berbeda untuk createLikeButtonTemplate dan createLikedButtonTemplate. 
    2. Alur negatif: sistem tidak memproses penyimpanan dan menyebabkan kegagalan jika menyimpan movie tanpa ID. OK



### TODO:

likeMovie.test

    await TestFactories.createLikeButtonPresenterWithMovie({ id: 1 });

unlikeMovie.test.js

    afterEach(async () => {
        await FavoriteMovieIdb.deleteMovie(1);
    });

    1. Memberi nama yang lebih berbeda untuk createLikeButtonTemplate dan createLikedButtonTemplate. OK
    2. Alur negatif: sistem tidak memproses penyimpanan dan menyebabkan kegagalan jika menyimpan movie tanpa ID. OK
