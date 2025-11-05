# Interactive-Easy-GPU-PV-Vietnamese-Version
Một nhánh fork đang trong quá trình develop của [Easy-GPU-PV](https://github.com/jamesstringerparsec/Easy-GPU-PV). Mục tiêu của project này là đơn giản hóa toàn bộ quy trình càng nhiều càng tốt. Script chính có tính tương tác khá nhiều, vì vậy mọi người không cần phải setting trước bất kỳ tham số (parameters) nào. Thay vào đó, các tham số có thể được chọn trong khi script đang chạy, giúp quy trình dễ dàng hơn nhiều.

![Administrator_-PowerShell-2023-03-21-16-38-00](https://user-images.githubusercontent.com/77991615/226651194-032db39b-291a-4cd4-a231-da5a215c9eee.gif)

![GPUSharingScriptDemo](https://github.com/lovetocode120/Interactive-Easy-GPU-PV-Vietnamese-Version/blob/main/GPUSharingScriptDemo.png)

![GPUSharingScriptDemoVideo](https://github.com/lovetocode120/Interactive-Easy-GPU-PV-Vietnamese-Version/blob/main/GPUSharingScriptDemoVideo.gif)

***Nội dung sau đây chủ yếu được trích từ dự án Easy-GPU-PV lúc đầu. Mình đã thực hiện một số sửa đổi, dịch sang ngôn ngữ tiếng Việt và improve để đảm bảo nội dung chính xác tình trạng hiện tại của dự án và cung cấp thông tin liên quan.***

GPU-PV cho phép mọi người phân vùng hoặc chia sẻ card đồ họa rời (discrete GPU) hay card đồ họa tích hợp (on-board GPU) và gắn bộ nhớ VRAM của GPU vật lý vào máy ảo Hyper-V. Nó cũng giống như công nghệ dùng trong WSL (Windows Subsystem for Linux), và máy ảo tạm thời Windows Sandbox.

Interactive-Easy-GPU-PV hướng đến mục tiêu tự động hóa các bước cần thiết để khởi động và chạy máy ảo được chia sẻ bộ nhớ VRAM từ GPU vật lý.  
Project này cung cấp những nội dung sau:
1) Tạo máy ảo có GPU tăng tốc đồ họa theo ý mọi người
2) Tự động cài Windows vào máy ảo Hyper-V
3) Phân vùng & chia sẻ bộ nhớ VRAM từ GPU mà mọi người chọn và sao chép các file driver cần thiết của GPU vào máy ảo
4) Cài đặt [Parsec](https://parsec.app) vào máy ảo Hyper-V. Parsec là app điều khiển máy tính từ xa có độ trễ thấp và support độ phân giải 4K60FPS, mình recommend sử dụng app này để connect với máy ảo. Mọi người có thể xài Parsec miễn phí, không vì mục đích thương mại. Để sử dụng Parsec cho mục đích thương mại, đăng ký tài khoản [Parsec For Teams](https://parsec.app/teams).
5) Cài đặt Windows App để cung cấp phiên điều khiển từ xa được bật GPU tăng tốc đồ họa 3D. Lưu ý: khả năng tăng tốc đồ họa 3D trong phiên làm việc từ xa Microsoft RDP sẽ kém hơn bằng phiên làm việc từ xa khi dùng Parsec.

### Các yêu cầu bắt buộc (PC & laptop requirements):
* Một chiếc PC hoặc laptop mạnh sử dụng hệ điều hành Windows 10 (20H1 trở lên) với phiên bản Pro, Enterprise, Education, hoặc Windows 11 Pro, Enterprise, Education và Windows Server 2022. Windows 11 và Windows Server 2022 được ưu tiên vì khả năng tương thích (pass requirements) tốt hơn. Máy vật lý và máy ảo phải có phiên bản Windows giống nhau, vì việc không giống version của hệ điều hành có thể gây ra lỗi tương thích, BSOD hoặc các vấn đề khác. Ví dụ: Win10 22H2 (vật lý) đi cùng với Win10 22H2 (máy ảo) hoặc Win11 25H2 (vật lý) đi cùng với Win11 25H2 (máy ảo).
* PC cá nhân hay laptop có gắn card đồ họa rời như NVIDIA/AMD hoặc card đồ họa tích hợp như Intel. GPU phải hỗ trợ mã hóa video phần cứng (NVIDIA NVENC, Intel Quicksync hoặc AMD AMF).
* Driver GPU phiên bản mới nhất từ ​​[Intel.com](https://www.intel.com/content/www/us/en/search.html#sort=relevancy&f:@tabfilter=[Downloads]&f:@stm_10385_en=[Graphics]), [AMD.com](https://www.amd.com/en/support) hoặc [NVIDIA.com](https://www.nvidia.com/download/index.aspx). Không nên dựa vào Device Manager hoặc Windows Update để cài driver. Điều quan trọng là mọi người phải đảm bảo đã cài đặt driver mới nhất cho GPU vật lý để tránh các sự cố tương thích và đảm bảo hiệu năng cao hơn.
* Tải file ISO Windows 10 mới nhất chính chủ của Microsoft [tại đây](https://www.microsoft.com/en-gb/software-download/windows10ISO) / Windows 11 [tại đây](https://www.microsoft.com/en-us/software-download/windows11) - Mình khuyên mọi người đừng sử dụng Media Creation Tool, nếu không có link tải ISO trực tiếp (static) nào có sẵn, hãy làm theo [hướng dẫn này.](https://www.nextofwindows.com/downloading-windows-10-iso-images-using-rufus)
* Công nghệ ảo hóa phải được bật (VT-x, AMD-V hoặc SVM Mode), ảo hóa nhập - xuất thuộc đơn vị quản lý bộ nhớ cũng phải được bật (IOMMU - Input/Output Memory Management Unit, ví dụ như: VT-d, AMD-Vi) trên bo mạch chủ (BIOS Setup) và [tính năng Hyper-V phải được bật hoàn toàn](https://docs.microsoft.com/en-us/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v) trên hệ điều hành Windows 10/11 (có yêu cầu khởi động lại).
* Cho phép các script PowerShell chạy trên hệ điều hành mà mọi người đang dùng: sử dụng Windows PowerShell hoặc PowerShell ISE - cách 1: gõ "Set-ExecutionPolicy unrestricted" trong PowerShell và mở app với tư cách là quản trị viên (admin). Cách 2: mọi người có thể vào Windows Settings > System > Advanced > Terminal > PowerShell (nếu như mọi người dùng Windows 11) hoặc Windows Settings > Update & Security > For developers > PowerShell (nếu như sử dụng Windows 10), bật "Change execution policy to allow local PowerShell scripts to run without signing. Require signing for remote scripts".

### Hướng dẫn cách làm:
Để bắt đầu với Interactive-Easy-GPU-PV, mọi người hãy làm theo các bước sau:
1) Hãy chắc chắn rằng máy vật lý của mọi người đạt đủ điều kiện được nhắc đến trong document này.
2) Tải file [Interactive-Easy-GPU-PV](https://github.com/jamesstringerparsec/Easy-GPU-PV/archive/refs/heads/main.zip) và giải nén vào thư mục trên máy tính.
3) Tìm và mở app Windows PowerShell ISE trên máy và chạy app như một quản trị viên (admin).
4) Tìm thư mục đã giải nén mà mọi người đã download xong và chạy script có tên "GPUP-management.ps1" hoặc "GPUPartitionSharingToVM-VN" (đối với những ai chưa biết tiếng Anh), click chuột phải và chọn "Run with PowerShell". Chọn "Tạo máy ảo mới với GPU tăng tốc đồ họa" hay "Create new VM with GPU acceleration" và setting các tham số cần thiết. Script sẽ bắt đầu tạo máy ảo sau khi setting tham số xong, có thể mất 5-10 phút tùy thuộc vào hệ thống của mọi người.
5) Sau khi tạo xong máy ảo, hãy mở và đăng nhập vào Parsec trên giao diện máy ảo Windows. Mọi người có thể dùng Parsec để connect tới máy ảo với độ phân giải lên đến 4K60FPS (cần có điều kiện mạng tốt - Wi-Fi 5 trở lên hoặc LAN).
6) Mọi việc đã xong, giờ mọi người có thể dùng luôn hoặc share cho bạn bè, hay ai đó không đủ điều kiện mua máy mới hoặc tiền nạp net cỏ.

### Nâng cấp driver GPU của máy ảo Hyper-V sau khi cập nhật driver GPU trên máy vật lý
Để đảm bảo máy ảo hoạt động tốt, điều quan trọng là phải cập nhật driver GPU bên trong VM sau khi cập nhật driver GPU thật trên máy vật lý. Để thực hiện việc này, hãy làm theo các bước sau:
1) Sau khi update driver GPU trên máy vật lý, restart lại một lần nữa.
2) Mở PowerShell ISE với tư cách admin, tìm thư mục giải nén Interactive-Easy-GPU-PV và chạy script "GPUP-management.ps1" hoặc "GPUPartitionSharingToVM-VN".
3) Bấm số 3: "Copy GPU Drivers from Host to VM" hoặc "Sao chép toàn bộ file driver GPU từ máy vật lý tới máy ảo". Nó sẽ copy toàn bộ file driver GPU được update từ máy vật lý tới máy ảo.

### Đặc biệt gửi lời cảm ơn tới:  
- [jamesstringerparsec](https://github.com/jamesstringerparsec/Easy-GPU-PV) đã tạo ra Easy-GPU-PV được coi là tài liệu gốc cũng như phần chính của tài liệu hướng dẫn này.
- [Hyper-ConvertImage](https://github.com/tabs-not-spaces/Hyper-ConvertImage) đã tạo ra phiên bản được nâng cấp của [Convert-WindowsImage](https://github.com/MicrosoftDocs/Virtualization-Documentation/tree/master/hyperv-tools/Convert-WindowsImage) hỗ trợ cho cả Windows 10 và Windows 11.
- [gawainXX](https://github.com/gawainXX) giúp đỡ [jamesstringerparsec](https://github.com/jamesstringerparsec/Easy-GPU-PV) trong việc thử nghiệm, chỉ ra lỗi và cải tiến tính năng.  

### Lưu ý:    
- Nếu mọi người cài đặt driver màn hình ảo của Parsec (Parsec Virtual Display Driver), sau khi đăng nhập vào Parsec trên máy ảo, hãy luôn sử dụng Parsec để kết nối với máy ảo. Luôn giữ cho driver Microsft Hyper-V Video tắt. Sử dụng RDP và chế độ nâng cao của Hyper-V (Enhanced Session) sẽ dẫn đến hiện tượng lỗi và màn hình đen trong Parsec. Sử dụng Parsec sẽ cho phép mọi người trải nghiệm với độ phân giải đạt 4K60FPS.
- Nếu gặp lỗi "ERROR: Cannot bind argument to parameter 'Path' because it is null.", điều này có nghĩa là mọi người đã sử dụng Media Creation Tool để tải và tạo file ISO. Thật tiếc là mọi người không thể sử dụng tool này nếu như không thấy liên kết tải xuống ISO trực tiếp trên trang web của Microsoft (static link), hãy làm theo [hướng dẫn này.](https://www.nextofwindows.com/downloading-windows-10-iso-images-using-rufus)  
- GPU trong máy vật lý của mọi người sẽ có tên đầu driver là Microsoft trong Device Manager, thay vì tên đầu driver gốc NVIDIA/Intel/AMD. Miễn là nó không có hình tam giác màu vàng ở trên cùng của thiết bị, thì nó vẫn hoạt động bình thường.
- Cần phải cắm một cáp dongle giả lập HDMI vào GPU để Parsec có thể ghi lại màn hình. Mọi người chỉ cần một cáp dongle giả lập này cho mỗi máy vật lý bất kể số lượng máy ảo.
- Nếu máy ảo của mọi người chạy nhanh, có thể máy ảo sẽ tự động chuyển thẳng đến màn hình đăng nhập trước khi driver card âm thanh ảo (VB-AUDIO Cable) và driver màn hình ảo khi Parsec được cài đặt, đừng quá lo. Driver này sẽ sớm được cài đặt trên máy ảo.  
- Màn hình có thể chuyển sang màu đen trong thời gian lên đến 10 giây trong những trường hợp thông báo UAC (User Account Control) xuất hiện, ứng dụng vào và ra khỏi chế độ toàn màn hình và khi chuyển đổi giữa các codec video trong Parsec - không thực sự chắc chắn tại sao điều này lại xảy ra, hiện tượng này chỉ xảy ra với máy GPU-P và có vẻ phục hồi nhanh hơn ở độ phân giải 1280x720.
- Kết xuất Vulkan không khả dụng và trò chơi hỗ trợ OpenGL có thể hoạt động hoặc không, tùy vào từng game mọi người cài trên máy ảo. [Link này](https://www.microsoft.com/en-us/p/opencl-and-opengl-compatibility-pack/9nqpsl29bfff?SilentAuth=1&wa=wsignin1.0#activetab=pivot:overviewtab) có thể giúp với một số ứng dụng hỗ trợ OpenGL.
- Nếu mọi người không có quyền quản trị viên trên máy ảo, điều đó có nghĩa là mọi người đã đặt tên người dùng và tên máy ảo giống nhau, hai tham số này phải khác nhau.
- Các card đồ họa thuộc kiến trúc AMD Polaris như RX 580 hiện không hỗ trợ mã hóa video phần cứng thông qua ảo hóa nửa phần GPU.
- Để tải và cài file ISO Windows sử dụng Rufus, phải bật "Check for updates".
