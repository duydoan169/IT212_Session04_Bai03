Phân Tích Lý Do Prompt Thô Dễ Bỏ Sót Lỗi:

Prompt "Mã này bị lỗi gì?" thất bại với loại lỗi logic này vì 3 lý do chính:
Code đúng cú pháp hoàn toàn: Trình biên dịch Java không phát hiện bất kỳ cảnh báo nào -

AI khi đọc code theo hướng kiểm tra cú pháp sẽ xác nhận code hợp lệ và dừng lại ở đó,

không đi sâu vào phân tích hành vi runtime.
Không có ca kiểm thử cụ thể: Không có đầu vào mẫu đi kèm nên AI không có cơ sở

để chạy thử logic trong đầu và nhận ra rằng j = i khiến arr[i] == arr[j] luôn đúng ngay lần đầu,

dẫn đến hàm luôn trả về arr[0] bất kể nội dung mảng.
Không có Role và Constraint: Không yêu cầu AI đóng vai kiểm toán viên code

hay ràng buộc phải phân tích logic từng vòng lặp -

AI mặc định phản hồi ở mức độ đọc lướt, không chủ động tìm điểm sai về mặt thuật toán.


Prompt sau khi tối ưu:

Hãy đóng vai một Code Auditor Java chuyên phân tích lỗi logic thuật toán,
đặc biệt là các lỗi không bị trình biên dịch phát hiện nhưng gây sai kết quả khi chạy thực tế.

Đây là mã nguồn cần kiểm tra:

---
public class DuplicateFinder {
public static Integer findDuplicate(int[] arr) {
for (int i = 0; i < arr.length; i++) {
for (int j = i; j < arr.length; j++) {
if (arr[i] == arr[j]) {
return arr[i];
}
}
}
return null;
}
}
---

Ca kiểm thử cụ thể cần phân tích:
- Đầu vào: arr = {1, 2, 3, 4} (mảng không có phần tử trùng lặp)
- Kết quả thực tế của code hiện tại: trả về 1
- Kết quả kỳ vọng đúng: trả về null

Hãy thực hiện 3 nhiệm vụ sau:

Nhiệm vụ 1: Giải thích chính xác tại sao với đầu vào {1, 2, 3, 4},
hàm luôn trả về 1 thay vì null -
truy vết từng bước của vòng lặp để chỉ ra dòng lệnh nào gây ra hành vi sai.

Nhiệm vụ 2: Viết lại hàm findDuplicate() đã sửa lỗi hoàn chỉnh,
sử dụng HashSet để giảm độ phức tạp từ O(N²) xuống O(N).

Nhiệm vụ 3: Viết JUnit 5 test case kiểm thử các trường hợp:
- Mảng không có phần tử trùng
- Mảng có phần tử trùng ở giữa
- Mảng có phần tử trùng ngay đầu
- Mảng rỗng hoặc một phần tử

Ràng buộc:
- Không thay đổi tên class và tên hàm
- Sử dụng Java 17
- Chú thích từng bước logic bằng tiếng Việt
- Trình bày mỗi nhiệm vụ trong một khối code markdown riêng biệt


Phản hồi từ AI:

Nhiệm vụ 1 - Truy vết lỗi logic:
TRUY VẾT VÒNG LẶP VỚI ĐẦU VÀO {1, 2, 3, 4}
=============================================

Vòng lặp ngoài: i = 0 → arr[i] = 1
Vòng lặp trong: j bắt đầu từ i = 0 → arr[j] = arr[0] = 1

Kiểm tra điều kiện: arr[i] == arr[j] → arr[0] == arr[0] → 1 == 1 → TRUE

Hàm lập tức return arr[0] = 1 mà không cần duyệt thêm.

Nguyên nhân gốc rễ: j khởi tạo bằng i thay vì i+1,
khiến mỗi phần tử luôn tự so sánh với chính nó ở lần đầu tiên của vòng trong.
Điều kiện arr[i] == arr[j] luôn đúng ngay lập tức ở bước j = i,
dẫn đến hàm luôn trả về arr[0] bất kể mảng có trùng lặp thực sự hay không.

Nhiệm vụ 2 - Mã nguồn đã sửa lỗi dùng HashSet:
javaimport java.util.HashSet;
import java.util.Set;

public class DuplicateFinder {

    public static Integer findDuplicate(int[] arr) {

        // Kiểm tra đầu vào null hoặc mảng quá ngắn để có thể có phần tử trùng
        if (arr == null || arr.length < 2) {
            return null;
        }

        // HashSet lưu các phần tử đã duyệt qua - thao tác add() và contains() đều là O(1)
        // Tổng độ phức tạp của toàn hàm: O(N) thay vì O(N²) như trước
        Set<Integer> seen = new HashSet<>();

        for (int num : arr) {
            // Nếu add() trả về false nghĩa là phần tử đã tồn tại trong set - tìm thấy trùng lặp
            if (!seen.add(num)) {
                return num;
            }
        }

        // Duyệt hết mảng mà không tìm thấy trùng lặp
        return null;
    }
}

Nhiệm vụ 3 - JUnit 5 Test Case:
javaimport org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.*;

class DuplicateFinderTest {

    // Mảng không có phần tử trùng - phải trả về null
    @Test
    void testNoDuplicate_shouldReturnNull() {
        assertNull(DuplicateFinder.findDuplicate(new int[]{1, 2, 3, 4}));
    }

    // Mảng có phần tử trùng ở giữa - phải trả về phần tử trùng đầu tiên gặp
    @Test
    void testDuplicateInMiddle_shouldReturnCorrectValue() {
        assertEquals(3, DuplicateFinder.findDuplicate(new int[]{1, 2, 3, 3, 4}));
    }

    // Mảng có phần tử trùng ngay từ đầu - phải trả về phần tử đó
    @Test
    void testDuplicateAtStart_shouldReturnCorrectValue() {
        assertEquals(1, DuplicateFinder.findDuplicate(new int[]{1, 1, 2, 3}));
    }

    // Mảng chỉ có một phần tử - không thể có trùng lặp, trả về null
    @Test
    void testSingleElement_shouldReturnNull() {
        assertNull(DuplicateFinder.findDuplicate(new int[]{5}));
    }

    // Mảng rỗng - trả về null
    @Test
    void testEmptyArray_shouldReturnNull() {
        assertNull(DuplicateFinder.findDuplicate(new int[]{}));
    }

    // Đầu vào null - trả về null, không được ném NullPointerException
    @Test
    void testNullInput_shouldReturnNull() {
        assertNull(DuplicateFinder.findDuplicate(null));
    }
}