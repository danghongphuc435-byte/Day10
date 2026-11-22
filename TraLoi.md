# Trả lời câu hỏi bổ sung — Buổi 10

### 1. Vì sao đổi `numColumns` của `FlatList` phải đổi cả `key`?
`FlatList` khởi tạo cấu trúc bố cục hàng/cột một lần duy nhất lúc mount nên không hỗ trợ đổi số cột trực tiếp khi đang chạy. Đổi `key` (ví dụ `key={String(numColumns)}`) buộc React huỷ `FlatList` cũ và dựng lại một `FlatList` mới với `numColumns` mới. Nếu bỏ `key`, khi bật/tắt Switch ứng dụng sẽ báo lỗi đỏ *"Changing numColumns on the fly is not supported"*.

### 2. Dữ liệu nào để trong `useState`, dữ liệu nào đưa vào Redux?
Dữ liệu cục bộ chỉ phục vụ một màn hình và không cần chia sẻ (như `keyword`, `isTile`, `category` ở `HomeScreen`) nên giữ trong `useState`. Dữ liệu cần đồng bộ giữa nhiều màn hình cùng đọc/ghi (như **giỏ hàng** dùng ở `DetailScreen`, `CartScreen` và badge tab; **yêu thích** dùng ở `DetailScreen` và `FavoritesScreen`) nên đưa vào Redux store.

### 3. Vì sao trong `createSlice` được sửa trực tiếp `state.items.push(...)`?
Redux Toolkit tích hợp sẵn thư viện **Immer** bên trong `createSlice`. Immer truyền vào reducer một bản nháp (Proxy draft) của state, ghi nhận mọi thao tác sửa trực tiếp rồi tự sinh ra một state mới bất biến mà không làm thay đổi state cũ. Ở component React bình thường không có Immer, nên sửa trực tiếp state sẽ không tạo tham chiếu mới khiến React không re-render.

### 4. Vì sao `selectIsFavorite(id)` trả về một hàm và chỉ lưu `id`?
`useAppSelector` yêu cầu truyền vào một hàm nhận `state: RootState`, nên `selectIsFavorite(id)` trả về một selector con để vừa khớp chữ ký hàm vừa truyền được tham số `id` vào bên trong. Chỉ lưu mảng `ids` thay vì cả đối tượng `Book` giúp tránh trùng lặp và lệch dữ liệu: thông tin sách (tên, giá, rating) luôn đọc từ một nguồn duy nhất nên khi sách cập nhật, màn Yêu thích tự động khớp theo.

### 5. Nếu bỏ `useCallback` ở `onSelect` thì `React.memo` trên `BookCard` còn tác dụng không?
Không còn tác dụng. Nếu không bọc `onSelect` trong `useCallback`, mỗi lần `HomeScreen` re-render sẽ tạo ra một tham chiếu hàm `onSelect` mới; `React.memo` so sánh nông props thấy `onSelect` đã đổi nên vẫn vẽ lại toàn bộ các thẻ `BookCard`.

### 6. (Bonus) Ba hành động của `createAsyncThunk` thay đổi những trường nào trong `booksSlice`?
- `fetchBooks.pending`: đặt `status = "loading"` và `error = null`.
- `fetchBooks.fulfilled`: đặt `status = "succeeded"` và gán `items = action.payload`.
- `fetchBooks.rejected`: đặt `status = "failed"` và gán `error = action.payload ?? "Không tải được dữ liệu"`.
