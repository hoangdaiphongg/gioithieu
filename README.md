<?php
session_start(); // Khởi động session

// Kiểm tra xem người dùng đã đăng nhập chưa
if (isset($_SESSION['username'])) {
    header('Location: trangdanhmucsukien.php'); // Chuyển hướng đến trang danh mục sự kiện nếu đã đăng nhập
    exit(); // Dừng thực thi mã sau khi chuyển hướng
}
?>
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link rel="stylesheet" href="css/css.css">
    <title>Các sự kiện gần đây</title>
</head>

<body>
    
    <!-- Thanh tìm kiếm -->
    <div class="thanhtimkiem">
        <form action="timkiem.php" method="GET">
            <input type="text" id="search" name="search" placeholder="Nhập tên sự kiện bạn đang tìm kiếm" required>
            <button type="submit">Tìm kiếm</button>
        </form>
    </div>

    <!-- Các ô sự kiện -->
    <div class="osukien">
        <h2>Các sự kiện gần đây</h2>
        <div class="event-container"> 
        <?php
        // Kết nối cơ sở dữ liệu
        include 'db_connection.php'; // Sử dụng file db_connection.php

        // Truy vấn lấy dữ liệu sự kiện
        $sql = "SELECT * FROM bangsukien"; // Thay đổi tên bảng nếu cần
        $result = mysqli_query($conn, $sql);

        // Kiểm tra nếu truy vấn thành công
        if ($result) {
            // Kiểm tra nếu có kết quả
            if (mysqli_num_rows($result) > 0) {
                // Lặp qua các sự kiện
                while ($row = mysqli_fetch_assoc($result)) {
                    echo '<div class="event-card">';

                    // Tách chuỗi hinh_anh và lấy ảnh đầu tiên
                    $images = explode(',', $row['hinh_anh']);
                    $first_image = trim($images[0]); // Lấy ảnh đầu tiên và loại bỏ khoảng trắng nếu có

                    echo '<img src="' . $first_image . '" alt="' . htmlspecialchars($row['ten_sukien']) . '" class="event-image">';
                    echo '<div class="event-info">';
                    echo '<h3>' . htmlspecialchars($row['ten_sukien']) . '</h3>';
                    echo '<p>Thời gian: ' . htmlspecialchars($row['thoi_gian']) . '</p>';
                    echo '<p>Ngày: ' . htmlspecialchars($row['ngay']) . '</p>';
                    echo '<p>Địa điểm: ' . htmlspecialchars($row['dia_diem']) . '</p>';
                    echo '<form method="GET" action="trangdangnhap.php">'; // Chuyển hướng đến trang đăng nhập
                    echo '<input type="hidden" name="event_id" value="' . $row['id'] . '">'; // Lưu ID sự kiện
                    echo '<button type="submit" name="register" class="btn-register">Đăng ký</button>'; // Nút đăng ký
                    echo '</form>'; // Kết thúc form
                    echo '</div>'; // Kết thúc div.event-info
                    echo '</div>'; // Kết thúc div.event-card
                }
            } else {
                echo '<p>Không có sự kiện nào.</p>';
            }
        } else {
            // Nếu truy vấn không thành công, in ra lỗi
            echo 'Lỗi truy vấn: ' . mysqli_error($conn);
        }

        // Đóng kết nối
        mysqli_close($conn);
        ?>
        </div> <!-- Kết thúc div.event-container -->
    </div> <!-- Kết thúc div.osukien -->

</body>
<?php
include 'chantrang.php';
include 'header.php'; 
?>
</html>
