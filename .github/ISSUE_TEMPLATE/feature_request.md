<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Công cụ chuyển đổi hóa đơn điện tử</title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/html2canvas/1.4.1/html2canvas.min.js"></script>
    <style>
        /* CSS đã được tối ưu hóa - giữ nguyên như trong mã gốc */
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }
        
        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            min-height: 100vh;
            padding: 20px;
        }
        
        .container {
            max-width: 1200px;
            margin: 0 auto;
            background: white;
            border-radius: 15px;
            box-shadow: 0 15px 35px rgba(0,0,0,0.1);
            overflow: hidden;
        }
        
        .header {
            background: linear-gradient(135deg, #4a6ee0 0%, #6a4ca6 100%);
            color: white;
            padding: 35px 30px;
            text-align: center;
            position: relative;
            overflow: hidden;
        }
        
        .header::before {
            content: '';
            position: absolute;
            top: 0;
            left: 0;
            right: 0;
            bottom: 0;
            background: url('data:image/svg+xml,<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 1000 100" fill="%23ffffff" opacity="0.1"><polygon points="0,0 1000,50 1000,100 0,100"/></svg>');
            background-size: cover;
        }
        
        .header h1 {
            font-size: 2.5em;
            margin-bottom: 15px;
            font-weight: 700;
            text-shadow: 0 2px 10px rgba(0,0,0,0.2);
        }
        
        .header p {
            font-size: 1.2em;
            opacity: 0.95;
            max-width: 600px;
            margin: 0 auto;
            line-height: 1.6;
        }
        
        .main-content {
            padding: 30px;
        }
        
        .tabs {
            display: flex;
            border-bottom: 3px solid #4a6ee0;
            margin-bottom: 25px;
            flex-wrap: wrap;
            background: #f8f9ff;
            border-radius: 12px 12px 0 0;
            padding: 5px;
        }
        
        .tab {
            padding: 15px 25px;
            cursor: pointer;
            border: none;
            background: transparent;
            border-radius: 8px;
            margin-right: 5px;
            font-weight: 600;
            transition: all 0.3s;
            margin-bottom: 5px;
            color: #555;
            position: relative;
            overflow: hidden;
        }
        
        .tab::before {
            content: '';
            position: absolute;
            top: 0;
            left: -100%;
            width: 100%;
            height: 100%;
            background: linear-gradient(135deg, #4a6ee0 0%, #6a4ca6 100%);
            transition: left 0.3s;
            z-index: -1;
        }
        
        .tab.active {
            color: white;
            transform: translateY(-2px);
            box-shadow: 0 5px 15px rgba(74, 110, 224, 0.3);
        }
        
        .tab.active::before {
            left: 0;
        }
        
        .tab-content {
            display: none;
            animation: fadeIn 0.5s ease;
        }
        
        .tab-content.active {
            display: block;
        }
        
        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(10px); }
            to { opacity: 1; transform: translateY(0); }
        }
        
        .upload-section {
            background: linear-gradient(135deg, #f8f9ff 0%, #eef1ff 100%);
            border: 3px dashed #4a6ee0;
            border-radius: 15px;
            padding: 40px;
            text-align: center;
            margin-bottom: 25px;
            transition: all 0.3s;
            position: relative;
            overflow: hidden;
        }
        
        .upload-section::before {
            content: '';
            position: absolute;
            top: -50%;
            left: -50%;
            width: 200%;
            height: 200%;
            background: linear-gradient(45deg, transparent, rgba(74, 110, 224, 0.1), transparent);
            transform: rotate(45deg);
            transition: all 0.6s;
        }
        
        .upload-section:hover::before {
            transform: rotate(45deg) translate(50%, 50%);
        }
        
        .upload-section:hover {
            border-color: #6a4ca6;
            transform: translateY(-3px);
            box-shadow: 0 10px 25px rgba(74, 110, 224, 0.15);
        }
        
        .file-input-wrapper {
            margin: 25px 0;
        }
        
        .file-input-label {
            display: inline-flex;
            align-items: center;
            gap: 10px;
            padding: 15px 30px;
            background: linear-gradient(135deg, #4a6ee0 0%, #6a4ca6 100%);
            color: white;
            border-radius: 10px;
            cursor: pointer;
            transition: all 0.3s;
            font-weight: 600;
            box-shadow: 0 5px 15px rgba(74, 110, 224, 0.3);
        }
        
        .file-input-label:hover {
            transform: translateY(-2px);
            box-shadow: 0 8px 20px rgba(74, 110, 224, 0.4);
        }
        
        .btn {
            padding: 15px 30px;
            border: none;
            border-radius: 10px;
            cursor: pointer;
            font-size: 16px;
            font-weight: 600;
            margin: 8px;
            transition: all 0.3s;
            position: relative;
            overflow: hidden;
        }
        
        .btn::before {
            content: '';
            position: absolute;
            top: 0;
            left: -100%;
            width: 100%;
            height: 100%;
            background: linear-gradient(90deg, transparent, rgba(255,255,255,0.3), transparent);
            transition: left 0.5s;
        }
        
        .btn:hover::before {
            left: 100%;
        }
        
        .btn-primary {
            background: linear-gradient(135deg, #4a6ee0 0%, #6a4ca6 100%);
            color: white;
            box-shadow: 0 5px 15px rgba(74, 110, 224, 0.3);
        }
        
        .btn-primary:hover {
            transform: translateY(-3px);
            box-shadow: 0 8px 20px rgba(74, 110, 224, 0.4);
        }
        
        .btn-success {
            background: linear-gradient(135deg, #28a745 0%, #20c997 100%);
            color: white;
            box-shadow: 0 5px 15px rgba(40, 167, 69, 0.3);
        }
        
        .btn-success:hover {
            transform: translateY(-3px);
            box-shadow: 0 8px 20px rgba(40, 167, 69, 0.4);
        }
        
        .btn-warning {
            background: linear-gradient(135deg, #ffc107 0%, #fd7e14 100%);
            color: white;
            box-shadow: 0 5px 15px rgba(255, 193, 7, 0.3);
        }
        
        .btn-warning:hover {
            transform: translateY(-3px);
            box-shadow: 0 8px 20px rgba(255, 193, 7, 0.4);
        }
        
        .btn-info {
            background: linear-gradient(135deg, #17a2b8 0%, #6f42c1 100%);
            color: white;
            box-shadow: 0 5px 15px rgba(23, 162, 184, 0.3);
        }
        
        .btn-info:hover {
            transform: translateY(-3px);
            box-shadow: 0 8px 20px rgba(23, 162, 184, 0.4);
        }
        
        .loading {
            display: none;
            text-align: center;
            padding: 30px;
            background: #f8f9ff;
            border-radius: 15px;
            margin: 20px 0;
        }
        
        .spinner {
            border: 4px solid #f3f3f3;
            border-top: 4px solid #4a6ee0;
            border-radius: 50%;
            width: 40px;
            height: 40px;
            animation: spin 1s linear infinite;
            margin: 0 auto 15px;
        }
        
        @keyframes spin {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }
        
        .link-area {
            display: none;
            margin-top: 25px;
            padding: 25px;
            background: linear-gradient(135deg, #e7f5e9 0%, #d4edda 100%);
            border-radius: 15px;
            border: 2px solid #c3e6cb;
            animation: slideUp 0.5s ease;
        }
        
        @keyframes slideUp {
            from { opacity: 0; transform: translateY(20px); }
            to { opacity: 1; transform: translateY(0); }
        }
        
        .link-area h3 {
            color: #155724;
            margin-bottom: 20px;
            font-size: 1.5rem;
            display: flex;
            align-items: center;
            gap: 10px;
        }
        
        .link-box {
            background: #f8f9fa;
            border: 2px solid #e9ecef;
            border-radius: 12px;
            padding: 20px;
            margin: 20px 0;
            word-break: break-all;
            font-family: 'Courier New', monospace;
            font-size: 0.95rem;
            cursor: text;
            user-select: all;
            transition: all 0.3s ease;
            position: relative;
            box-shadow: 0 3px 10px rgba(0,0,0,0.08);
        }
        
        .link-box:hover {
            border-color: #4a6ee0;
            transform: translateY(-2px);
            box-shadow: 0 5px 15px rgba(74, 110, 224, 0.15);
        }
        
        .action-buttons {
            display: flex;
            gap: 15px;
            flex-wrap: wrap;
            margin: 25px 0;
        }
        
        .copy-btn, .open-btn {
            flex: 1;
            min-width: 140px;
            padding: 15px 25px;
            border: none;
            border-radius: 12px;
            cursor: pointer;
            font-size: 1rem;
            font-weight: 600;
            transition: all 0.3s ease;
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 10px;
            box-shadow: 0 5px 15px rgba(0,0,0,0.1);
        }
        
        .copy-btn {
            background: linear-gradient(135deg, #28a745 0%, #20c997 100%);
            color: white;
        }
        
        .copy-btn:hover {
            transform: translateY(-3px);
            box-shadow: 0 8px 20px rgba(40, 167, 69, 0.3);
        }
        
        .copy-btn.copied {
            background: linear-gradient(135deg, #20c997 0%, #17a2b8 100%);
        }
        
        .open-btn {
            background: linear-gradient(135deg, #4a6ee0 0%, #6a4ca6 100%);
            color: white;
            text-decoration: none;
        }
        
        .open-btn:hover {
            transform: translateY(-3px);
            box-shadow: 0 8px 20px rgba(74, 110, 224, 0.3);
        }
        
        .info-box {
            background: linear-gradient(135deg, #fff3cd 0%, #ffeaa7 100%);
            border: 2px solid #ffeaa7;
            border-radius: 12px;
            padding: 25px;
            margin: 25px 0;
        }
        
        .info-box h4 {
            color: #856404;
            margin-bottom: 20px;
            font-size: 1.3rem;
            display: flex;
            align-items: center;
            gap: 10px;
        }
        
        .info-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
            gap: 20px;
        }
        
        .info-item {
            display: flex;
            flex-direction: column;
            padding: 15px;
            background: rgba(255,255,255,0.7);
            border-radius: 10px;
            border-left: 4px solid #4a6ee0;
        }
        
        .info-label {
            font-weight: 600;
            color: #4a6ee0;
            font-size: 0.95rem;
            margin-bottom: 8px;
        }
        
        .info-value {
            color: #2c3e50;
            font-size: 1.05rem;
            font-weight: 600;
        }
        
        .status-message {
            padding: 20px;
            border-radius: 12px;
            margin: 20px 0;
            display: none;
            animation: slideIn 0.3s ease;
            font-weight: 600;
        }
        
        .status-success {
            background: linear-gradient(135deg, #d4edda 0%, #c3e6cb 100%);
            color: #155724;
            border: 2px solid #c3e6cb;
        }
        
        .status-error {
            background: linear-gradient(135deg, #f8d7da 0%, #f5c6cb 100%);
            color: #721c24;
            border: 2px solid #f5c6cb;
        }
        
        .instructions {
            background: linear-gradient(135deg, #e7f3ff 0%, #d6e4ff 100%);
            border-left: 5px solid #4a6ee0;
            padding: 20px;
            border-radius: 0 12px 12px 0;
            margin-top: 25px;
        }
        
        .instructions h4 {
            color: #4a6ee0;
            margin-bottom: 15px;
            font-size: 1.2rem;
            display: flex;
            align-items: center;
            gap: 10px;
        }
        
        .instructions ol {
            padding-left: 25px;
        }
        
        .instructions li {
            margin-bottom: 12px;
            color: #555;
            line-height: 1.6;
        }
        
        .file-info {
            text-align: center;
            margin-top: 20px;
            padding: 15px;
            background: #e9ecef;
            border-radius: 10px;
            font-size: 0.95rem;
            color: #666;
        }
        
        .debug-info {
            background: #f8f9fa;
            border: 2px solid #dee2e6;
            border-radius: 12px;
            padding: 20px;
            margin-top: 20px;
            font-family: 'Courier New', monospace;
            font-size: 0.85rem;
            display: none;
        }
        
        @keyframes pulse {
            0% { transform: scale(1); }
            50% { transform: scale(1.05); }
            100% { transform: scale(1); }
        }
        
        .pulse {
            animation: pulse 0.5s ease-in-out;
        }
        
        .module-misa {
            background: linear-gradient(135deg, #4a6ee0 0%, #6a4ca6 100%);
            color: white;
            padding: 25px;
            border-radius: 15px;
            margin-bottom: 25px;
            text-align: center;
            box-shadow: 0 8px 25px rgba(74, 110, 224, 0.3);
            position: relative;
            overflow: hidden;
        }
        
        .module-misa::before {
            content: '';
            position: absolute;
            top: -50%;
            left: -50%;
            width: 200%;
            height: 200%;
            background: linear-gradient(45deg, transparent, rgba(255,255,255,0.1), transparent);
            transform: rotate(45deg);
        }
        
        .module-misa h3 {
            margin-bottom: 15px;
            font-size: 1.6rem;
            font-weight: 700;
        }
        
        .module-misa .module-info {
            background: rgba(255, 255, 255, 0.2);
            padding: 15px;
            border-radius: 10px;
            font-weight: 600;
            margin-top: 15px;
            backdrop-filter: blur(10px);
        }
        
        .file-upload-area {
            border: 3px dashed #4a6ee0;
            border-radius: 20px;
            padding: 50px 30px;
            text-align: center;
            cursor: pointer;
            margin-bottom: 25px;
            transition: all 0.3s ease;
            background: linear-gradient(135deg, #fafbfc 0%, #f8f9ff 100%);
            position: relative;
            overflow: hidden;
        }
        
        .file-upload-area::before {
            content: '';
            position: absolute;
            top: -50%;
            left: -50%;
            width: 200%;
            height: 200%;
            background: linear-gradient(45deg, transparent, rgba(74, 110, 224, 0.1), transparent);
            transform: rotate(45deg);
            transition: all 0.6s;
        }
        
        .file-upload-area:hover::before {
            transform: rotate(45deg) translate(50%, 50%);
        }
        
        .file-upload-area:hover {
            border-color: #6a4ca6;
            background: linear-gradient(135deg, #f8f9ff 0%, #f0f4ff 100%);
            transform: translateY(-3px);
            box-shadow: 0 15px 30px rgba(74, 110, 224, 0.15);
        }
        
        .file-upload-area.dragover {
            border-color: #4a6ee0;
            background: #f0f4ff;
            transform: scale(1.02);
        }
        
        .upload-icon {
            font-size: 64px;
            margin-bottom: 20px;
            color: #4a6ee0;
            filter: drop-shadow(0 5px 10px rgba(74, 110, 224, 0.3));
        }
        
        .invoice-details-misa {
            background: linear-gradient(135deg, #fff3cd 0%, #ffeaa7 100%);
            padding: 20px;
            border-radius: 15px;
            border-left: 5px solid #ffc107;
            margin-top: 20px;
            display: none;
            animation: slideUp 0.5s ease;
        }
        
        .invoice-details-misa h5 {
            color: #856404;
            margin-bottom: 15px;
            display: flex;
            align-items: center;
            gap: 10px;
            font-size: 1.2rem;
            font-weight: 600;
        }
        
        .detail-item {
            display: flex;
            justify-content: space-between;
            margin-bottom: 10px;
            font-size: 1rem;
            padding: 8px 0;
            border-bottom: 1px solid rgba(133, 100, 4, 0.2);
        }
        
        .detail-item:last-child {
            border-bottom: none;
        }
        
        .detail-label {
            font-weight: 600;
            color: #856404;
        }
        
        .detail-value {
            color: #2c3e50;
            font-weight: 700;
        }
        
        .processing-misa {
            display: none;
            text-align: center;
            padding: 25px;
            background: #f8f9fa;
            border-radius: 15px;
            margin: 20px 0;
            animation: pulse 2s infinite;
        }
        
        .spinner-misa {
            border: 4px solid #f3f3f3;
            border-top: 4px solid #4a6ee0;
            border-radius: 50%;
            width: 45px;
            height: 45px;
            animation: spin 1s linear infinite;
            margin: 0 auto 15px;
        }
        
        .smart-download-section {
            background: linear-gradient(135deg, #e3f2fd 0%, #bbdefb 100%);
            border-radius: 15px;
            padding: 25px;
            margin: 20px 0;
            border: 2px solid #90caf9;
        }
        
        .download-option {
            background: white;
            padding: 20px;
            border-radius: 12px;
            margin-bottom: 20px;
            border-left: 5px solid #4a6ee0;
            box-shadow: 0 5px 15px rgba(0,0,0,0.08);
        }
        
        .download-option h5 {
            color: #2c3e50;
            margin-bottom: 15px;
            font-size: 1.2rem;
            display: flex;
            align-items: center;
            gap: 10px;
        }
        
        .option-badge {
            background: linear-gradient(135deg, #4a6ee0 0%, #6a4ca6 100%);
            color: white;
            padding: 5px 12px;
            border-radius: 20px;
            font-size: 0.8rem;
            font-weight: 600;
        }
        
        .option-warning {
            background: linear-gradient(135deg, #ffc107 0%, #fd7e14 100%);
        }
        
        .pdf-download-btn, .direct-pdf-link, .direct-lookup-btn {
            display: inline-flex;
            align-items: center;
            gap: 10px;
            padding: 12px 25px;
            background: linear-gradient(135deg, #4a6ee0 0%, #6a4ca6 100%);
            color: white;
            text-decoration: none;
            border-radius: 10px;
            font-weight: 600;
            transition: all 0.3s;
            box-shadow: 0 5px 15px rgba(74, 110, 224, 0.3);
            border: none;
            cursor: pointer;
        }
        
        .pdf-download-btn:hover, .direct-pdf-link:hover, .direct-lookup-btn:hover {
            transform: translateY(-2px);
            box-shadow: 0 8px 20px rgba(74, 110, 224, 0.4);
        }
        
        .provider-badge {
            background: #28a745;
            color: white;
            padding: 2px 8px;
            border-radius: 12px;
            font-size: 0.7rem;
            margin-left: 8px;
        }
        
        .lookup-form {
            padding: 20px;
        }
        
        .form-group {
            margin-bottom: 20px;
        }
        
        .form-control {
            width: 100%;
            padding: 12px 15px;
            border: 2px solid #e9ecef;
            border-radius: 10px;
            font-size: 1rem;
            transition: all 0.3s;
        }
        
        .form-control:focus {
            border-color: #4a6ee0;
            box-shadow: 0 0 0 3px rgba(74, 110, 224, 0.1);
            outline: none;
        }
        
        .provider-info, .lookup-result {
            background: #f8f9fa;
            padding: 20px;
            border-radius: 12px;
            margin: 15px 0;
            border-left: 4px solid #4a6ee0;
        }
        
        .lookup-success {
            background: linear-gradient(135deg, #d4edda 0%, #c3e6cb 100%);
            border-left-color: #28a745;
        }
        
        .lookup-error {
            background: linear-gradient(135deg, #f8d7da 0%, #f5c6cb 100%);
            border-left-color: #dc3545;
        }
        
        .direct-lookup-section {
            background: linear-gradient(135deg, #e3f2fd 0%, #bbdefb 100%);
            padding: 20px;
            border-radius: 12px;
            margin: 15px 0;
        }
        
        .direct-pdf-info {
            background: linear-gradient(135deg, #d4edda 0%, #c3e6cb 100%);
            padding: 20px;
            border-radius: 12px;
            margin: 15px 0;
            border-left: 4px solid #28a745;
        }
        
        .provider-table {
            width: 100%;
            border-collapse: collapse;
            margin: 15px 0;
        }
        
        .provider-table th {
            background: #4a6ee0;
            color: white;
            padding: 12px;
            text-align: left;
        }
        
        .provider-table td {
            padding: 12px;
            border-bottom: 1px solid #dee2e6;
        }
        
        .provider-table tr:hover {
            background: #f8f9ff;
        }
        
        .provider-link {
            color: #4a6ee0;
            text-decoration: none;
            font-weight: 600;
        }
        
        .provider-link:hover {
            text-decoration: underline;
        }
        
        .search-box {
            margin-bottom: 20px;
        }
        
        .search-box input {
            width: 100%;
            padding: 12px 15px;
            border: 2px solid #e9ecef;
            border-radius: 10px;
            font-size: 1rem;
        }
        
        .api-loading {
            display: inline-block;
            width: 12px;
            height: 12px;
            border: 2px solid #fff;
            border-top: 2px solid transparent;
            border-radius: 50%;
            animation: spin 1s linear infinite;
            margin-left: 8px;
        }
        
        .module-bkav {
            background: linear-gradient(135deg, #2c3e50 0%, #3498db 100%);
            color: white;
            padding: 25px;
            border-radius: 15px;
            margin-bottom: 25px;
            text-align: center;
            box-shadow: 0 8px 25px rgba(44, 62, 80, 0.3);
            position: relative;
            overflow: hidden;
        }
        
        .module-bkav::before {
            content: '';
            position: absolute;
            top: -50%;
            left: -50%;
            width: 200%;
            height: 200%;
            background: linear-gradient(45deg, transparent, rgba(255,255,255,0.1), transparent);
            transform: rotate(45deg);
        }
        
        .module-bkav h3 {
            margin-bottom: 15px;
            font-size: 1.6rem;
            font-weight: 700;
        }
        
        .module-bkav .module-info {
            background: rgba(255, 255, 255, 0.2);
            padding: 15px;
            border-radius: 10px;
            font-weight: 600;
            margin-top: 15px;
            backdrop-filter: blur(10px);
        }
        
        .xml-input-container {
            margin-bottom: 25px;
        }
        
        .xml-input {
            width: 100%;
            height: 200px;
            padding: 15px;
            border: 2px solid #e1e8ed;
            border-radius: 10px;
            font-family: 'Courier New', monospace;
            font-size: 14px;
            resize: vertical;
            transition: border-color 0.3s ease;
        }
        
        .xml-input:focus {
            outline: none;
            border-color: #3498db;
            box-shadow: 0 0 0 3px rgba(52, 152, 219, 0.1);
        }
        
        .validation-message {
            padding: 12px;
            border-radius: 8px;
            margin-bottom: 15px;
            text-align: center;
            font-weight: 600;
        }
        
        .validation-success {
            background: #d5f4e6;
            color: #27ae60;
            border: 2px solid #27ae60;
        }
        
        .validation-error {
            background: #fadbd8;
            color: #e74c3c;
            border: 2px solid #e74c3c;
        }
        
        .file-upload-bkav {
            position: relative;
            margin-bottom: 15px;
        }
        
        .file-upload-bkav-input {
            width: 100%;
            padding: 10px;
            border: 2px dashed #3498db;
            border-radius: 8px;
            text-align: center;
            cursor: pointer;
            transition: all 0.3s ease;
        }
        
        .file-upload-bkav-input:hover {
            background: #e3f2fd;
        }
        
        .file-upload-bkav-input input {
            display: none;
        }
        
        .status-indicator {
            display: inline-block;
            width: 10px;
            height: 10px;
            border-radius: 50%;
            margin-right: 8px;
        }
        
        .status-ready {
            background: #27ae60;
        }
        
        .status-warning {
            background: #f39c12;
        }
        
        .status-error {
            background: #e74c3c;
        }
        
        .bkav-result-container {
            background: #f8f9fa;
            border-radius: 10px;
            padding: 25px;
            border-left: 5px solid #3498db;
            display: none;
        }
        
        .bkav-download-link {
            display: block;
            background: white;
            padding: 15px;
            border-radius: 8px;
            border: 2px dashed #3498db;
            color: #3498db;
            text-decoration: none;
            word-break: break-all;
            margin-bottom: 15px;
            transition: all 0.3s ease;
        }
        
        .bkav-download-link:hover {
            background: #e3f2fd;
            border-color: #2980b9;
        }
        
        .bkav-link-info {
            background: #e8f5e8;
            padding: 15px;
            border-radius: 8px;
            border-left: 4px solid #27ae60;
            margin-top: 15px;
        }
        
        .bkav-info-item {
            display: flex;
            justify-content: space-between;
            margin-bottom: 8px;
            padding-bottom: 8px;
            border-bottom: 1px solid #d5dbdb;
        }
        
        .bkav-info-item:last-child {
            margin-bottom: 0;
            border-bottom: none;
        }
        
        .bkav-info-label {
            font-weight: 600;
            color: #2c3e50;
        }
        
        .bkav-info-value {
            color: #27ae60;
            font-family: 'Courier New', monospace;
        }
        
        .msttcgp-footer {
            background: #2c3e50;
            color: white;
            padding: 20px;
            text-align: center;
            margin-top: 30px;
            border-radius: 0 0 15px 15px;
        }
        
        .msttcgp-footer h4 {
            margin-bottom: 15px;
            font-size: 1.2rem;
        }
        
        .msttcgp-list {
            display: flex;
            flex-wrap: wrap;
            justify-content: center;
            gap: 15px;
            margin-top: 15px;
        }
        
        .msttcgp-item {
            background: rgba(255, 255, 255, 0.1);
            padding: 10px 15px;
            border-radius: 8px;
            font-size: 0.9rem;
        }
        
        .msttcgp-module {
            background: rgba(52, 152, 219, 0.3);
            border-left: 4px solid #3498db;
        }
        
        .modal-overlay {
            display: none;
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0, 0, 0, 0.7);
            z-index: 1000;
            overflow-y: auto;
            padding: 20px;
            backdrop-filter: blur(5px);
        }
        
        .modal-content {
            position: relative;
            background: white;
            margin: 0 auto;
            max-width: 800px;
            box-shadow: 0 20px 50px rgba(0,0,0,0.3);
            border-radius: 15px;
            overflow: hidden;
            animation: modalSlideIn 0.4s ease;
        }
        
        @keyframes modalSlideIn {
            from { opacity: 0; transform: translateY(-30px); }
            to { opacity: 1; transform: translateY(0); }
        }
        
        .modal-header {
            padding: 20px 25px;
            background: linear-gradient(135deg, #4a6ee0 0%, #6a4ca6 100%);
            color: white;
            display: flex;
            justify-content: space-between;
            align-items: center;
        }
        
        .modal-header h2 {
            font-size: 1.6em;
            margin: 0;
            font-weight: 700;
        }
        
        .close-btn {
            background: none;
            border: none;
            color: white;
            font-size: 28px;
            cursor: pointer;
            padding: 0;
            width: 35px;
            height: 35px;
            display: flex;
            align-items: center;
            justify-content: center;
            border-radius: 50%;
            transition: background 0.3s;
        }
        
        .close-btn:hover {
            background: rgba(255,255,255,0.2);
        }
        
        .modal-body {
            padding: 0;
        }
        
        .modal-footer {
            padding: 20px 25px;
            background: #f8f9ff;
            display: flex;
            justify-content: space-between;
            align-items: center;
            border-top: 1px solid #dee2e6;
        }
        
        /* Hóa đơn GTGT Styles với lề 1.5cm và font 10pt - CẬP NHẬT TEMPLATE MỚI */
        .invoice-gtgt-container {
            width: 210mm;
            min-height: 297mm;
            margin: 0 auto;
            background: white;
            font-family: "Times New Roman", Times, serif;
            font-size: 10pt;
            color: #000000;
            position: relative;
            padding: 15mm 15mm 15mm 15mm;
            page-break-after: always;
            box-sizing: border-box;
            /* Hình nền chìm cho hóa đơn - ĐÃ ĐIỀU CHỈNH ĐỘ TƯƠNG PHẢN */
            background-image: url('data:image/svg+xml;utf8,<svg xmlns="http://www.w3.org/2000/svg" width="200" height="200" viewBox="0 0 200 200"><rect width="200" height="200" fill="%23f5f5f5" opacity="0.1"/><text x="100" y="100" font-family="Arial" font-size="20" text-anchor="middle" fill="%23000000" opacity="0.05">HÓA ĐƠN ĐIỆN TỬ</text></svg>');
            background-repeat: repeat;
            background-position: center;
            background-size: 200px 200px;
            border: 1px solid #cccccc;
            box-shadow: 0 0 10px rgba(0,0,0,0.1);
        }
        
        .invoice-gtgt-box-large {
            margin-left: auto;
            margin-right: auto;
            border-collapse: collapse;
            padding: 5px;
            border: 1px solid #000000;
            width: 100%;
            color: #000000;
        }
        
        .invoice-gtgt-box-small {
            border-collapse: collapse;
            padding: 5px;
            border: 1px solid #000000;
            color: #000000;
        }
        
        .invoice-gtgt-label-normal {
            padding: 2px;
            color: #000000;
        }
        
        .invoice-gtgt-label-italic {
            padding: 2px;
            font-style: italic;
            color: #000000;
        }
        
        .invoice-gtgt-label-bold {
            font-weight: bold;
            padding: 2px;
            color: #000000;
        }
        
        .invoice-gtgt-item-normal {
            font-weight: normal;
            padding: 2px;
            color: #000000;
        }
        
        .invoice-gtgt-item-bold {
            font-weight: bold;
            padding: 2px;
            color: #000000;
        }
        
        .invoice-gtgt-border-bottom {
            border-bottom: 2px solid #000000;
        }
        
        .invoice-gtgt-text-center {
            text-align: center;
        }
        
        .invoice-gtgt-text-right {
            text-align: right;
        }
        
        .invoice-gtgt-width-100 {
            width: 100%;
        }
        
        .invoice-gtgt-vertical-top {
            vertical-align: top;
        }
        
        .invoice-gtgt-no-border {
            border: none !important;
        }
        
        .page-number {
            position: absolute;
            bottom: 15mm;
            right: 15mm;
            font-size: 10pt;
            color: #000000;
            font-weight: bold;
        }

        /* Container cho ảnh chụp */
        .capture-container {
            position: absolute;
            left: -9999px;
            top: -9999px;
            width: 210mm;
            background: white;
            font-family: "Times New Roman", Times, serif !important;
            background-image: url('data:image/svg+xml;utf8,<svg xmlns="http://www.w3.org/2000/svg" width="200" height="200" viewBox="0 0 200 200"><rect width="200" height="200" fill="%23f5f5f5" opacity="0.1"/><text x="100" y="100" font-family="Arial" font-size="20" text-anchor="middle" fill="%23000000" opacity="0.05">HÓA ĐƠN ĐIỆN TỬ</text></svg>');
            background-repeat: repeat;
            background-position: center;
            background-size: 200px 200px;
        }

        .force-times-new-roman {
            font-family: "Times New Roman", Times, serif !important;
        }

        /* Pagination controls */
        .pagination-controls {
            display: flex;
            justify-content: center;
            align-items: center;
            margin: 20px 0;
            gap: 10px;
        }
        
        .pagination-btn {
            padding: 8px 16px;
            background: #4a6ee0;
            color: white;
            border: none;
            border-radius: 5px;
            cursor: pointer;
            font-weight: 600;
        }
        
        .pagination-btn:disabled {
            background: #cccccc;
            cursor: not-allowed;
        }
        
        .page-info {
            font-weight: 600;
            color: #4a6ee0;
        }

        /* Print styles for multi-page */
        @media print {
            .invoice-gtgt-container {
                page-break-after: always;
                margin: 0;
                padding: 15mm;
                width: 100%;
                height: 297mm;
            }
            
            .invoice-gtgt-container:last-child {
                page-break-after: auto;
            }
        }

        /* Invoice Preview Styles */
        .invoice-preview {
            background: white;
            border: 2px solid #eef1ff;
            border-radius: 15px;
            padding: 30px;
            margin-top: 25px;
            display: none;
        }

        .processing-status {
            text-align: center;
            padding: 20px;
            background: #f8f9ff;
            border-radius: 15px;
            margin: 20px 0;
            display: none;
        }

        .progress-container {
            width: 100%;
            background-color: #f1f1f1;
            border-radius: 10px;
            margin: 20px 0;
            overflow: hidden;
        }
        
        .progress-bar {
            width: 0%;
            height: 20px;
            background: linear-gradient(135deg, #4a6ee0 0%, #6a4ca6 100%);
            border-radius: 10px;
            transition: width 0.3s ease;
            display: flex;
            align-items: center;
            justify-content: center;
            color: white;
            font-weight: bold;
        }

        /* Footer styles for invoice */
        .footer {
            margin-top: 10px;
            padding: 10px;
            border-top: 1px solid #000;
            font-size: 9pt;
            text-align: center;
        }
        
        .cqt-info {
            margin: 10px 0;
            padding: 8px;
            border: 1px solid #000;
            background: #f9f9f9;
        }
        
        .cqt-code {
            font-weight: bold;
            font-size: 10pt;
            margin: 5px 0;
        }

        @media (max-width: 768px) {
            .header h1 {
                font-size: 2rem;
            }
            
            .header p {
                font-size: 1rem;
            }
            
            .upload-section {
                padding: 25px 15px;
            }
            
            .action-buttons {
                flex-direction: column;
            }
            
            .info-grid {
                grid-template-columns: 1fr;
            }
            
            .modal-content {
                margin: 10px;
            }
            
            .tabs {
                flex-direction: column;
            }
            
            .tab {
                margin-bottom: 5px;
                border-radius: 8px;
            }

            .invoice-gtgt-container {
                width: 100%;
                padding: 10mm;
            }
        }
        
        .btn:disabled, .btn:disabled:hover {
            background: #6c757d !important;
            cursor: not-allowed !important;
            transform: none !important;
            box-shadow: none !important;
            opacity: 0.6;
        }
        
        .direct-pdf-link.disabled, .direct-lookup-btn.disabled {
            background: #6c757d !important;
            cursor: not-allowed !important;
            transform: none !important;
            box-shadow: none !important;
            opacity: 0.6;
            pointer-events: none;
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="header">
            <h1>Công cụ chuyển đổi hóa đơn điện tử</h1>
            <p>Chuyển đổi file XML hóa đơn điện tử sang PDF và tạo link tải PDF</p>
        </div>
        
        <div class="main-content">
            <div class="tabs">
                <div class="tab active" data-tab="link-generator" id="module-tab">Vĩnh Hy PDF Generator</div>
                <div class="tab" data-tab="module-misa" id="module-misa-tab">Module MISA</div>
                <div class="tab" data-tab="module-bkav" id="module-bkav-tab">Module BKAV</div>
                <div class="tab" data-tab="invoice-lookup" id="lookup-tab">🔍 Tra cứu hóa đơn</div>
                <div class="tab" data-tab="invoice-gtgt">Hóa đơn GTGT</div>
            </div>
            
            <!-- Tab 1: Tạo Link PDF từ XML -->
            <div class="tab-content active" id="link-generator">
                <div class="upload-section">
                    <h3>Upload File XML Hóa Đơn</h3>
                    <p>Click để chọn file hoặc kéo thả file XML vào đây</p>
                    <div class="file-input-wrapper">
                        <input type="file" id="xmlFileLink" accept=".xml" style="display: none;">
                        <label for="xmlFileLink" class="file-input-label">
                            <span>📁 Chọn File XML</span>
                        </label>
                    </div>
                    <p id="fileNameLink" style="margin: 15px 0; color: #666;"></p>
                    <button id="generateLinkBtn" class="btn btn-primary">🔗 Tạo Link PDF</button>
                    <div class="file-info" id="fileInfo"></div>
                </div>
                
                <div class="loading" id="loadingLink">
                    <div class="spinner"></div>
                    <p>Đang xử lý file XML...</p>
                </div>
                
                <div class="status-message" id="statusMessage"></div>
                
                <div class="link-area" id="linkArea">
                    <h3>✅ Link PDF Đã Sẵn Sàng!</h3>
                    <p>Copy link dưới đây và dán vào trình duyệt để tải PDF:</p>
                    
                    <div class="link-box" id="pdfLink" onclick="selectLinkText()" tabindex="0">
                        https://ehoadondientu.com/Account/GenerateFile.aspx?r=...
                    </div>

                    <div class="action-buttons">
                        <button class="copy-btn" onclick="copyLink()" id="copyButton">
                            📋 Copy Link
                        </button>
                        <a href="#" class="open-btn" id="downloadLink" target="_blank">
                            🌐 Mở Link
                        </a>
                    </div>

                    <div class="info-box">
                        <h4>📋 Thông Tin Hóa Đơn</h4>
                        <div class="info-grid" id="invoiceInfo">
                            <!-- Thông tin sẽ được thêm bằng JavaScript -->
                        </div>
                    </div>

                    <button class="btn btn-info" onclick="toggleDebug()" style="margin-top: 10px;">
                        🔧 Debug Info
                    </button>

                    <div class="debug-info" id="debugInfo">
                        <!-- Thông tin debug sẽ được thêm ở đây -->
                    </div>

                    <div class="instructions">
                        <h4>📝 Hướng Dẫn Sử Dụng</h4>
                        <ol>
                            <li>Click "Copy Link" để sao chép đường dẫn</li>
                            <li>Dán link vào thanh địa chỉ trình duyệt</li>
                            <li>Nhấn Enter để tải file PDF hóa đơn</li>
                            <li>Hoặc click "Mở Link" để mở trực tiếp</li>
                        </ol>
                    </div>
                </div>
            </div>
            
            <!-- Tab 2: Module MISA -->
            <div class="tab-content" id="module-misa">
                <div class="module-misa">
                    <h3>📁 Tool Tạo Link PDF từ File XML</h3>
                    <p>Module MISA - Phiên bản chính thức</p>
                    <div class="module-info">
                        <strong>Mã nhận dạng module:</strong> 0101243150
                    </div>
                </div>
                
                <div class="file-upload-area" id="file-upload-area" onclick="document.getElementById('file-input-misa').click()">
                    <div class="upload-icon">📁</div>
                    <h3>Kéo thả file XML vào đây</h3>
                    <p>hoặc click để chọn file từ máy tính</p>
                    <p class="file-types">Hỗ trợ định dạng: .xml, .txt</p>
                </div>
                
                <input type="file" id="file-input-misa" class="file-input" accept=".xml,.txt" style="display: none;">
                
                <div id="file-info-misa" class="file-info">
                    <div class="file-name" id="file-name-misa">Tên file</div>
                    <div class="file-size" id="file-size-misa">Kích thước</div>
                </div>

                <div id="processing-misa" class="processing-misa">
                    <div class="spinner-misa"></div>
                    <p>Đang phân tích file XML...</p>
                </div>
                
                <button class="btn btn-primary" id="process-btn-misa" style="width: 100%; margin-bottom: 20px;" disabled>
                    <span>🔍 Phân Tích XML & Tạo Link PDF</span>
                </button>

                <!-- Invoice Details -->
                <div id="invoice-details-misa" class="invoice-details-misa">
                    <h5>📋 Thông tin hóa đơn đã nhận dạng</h5>
                    <div class="detail-item">
                        <span class="detail-label">MST Người bán:</span>
                        <span class="detail-value" id="detail-sellerTaxCode">-</span>
                    </div>
                    <div class="detail-item">
                        <span class="detail-label">Transaction ID:</span>
                        <span class="detail-value" id="detail-transactionId">-</span>
                    </div>
                    <div class="detail-item">
                        <span class="detail-label">Số hóa đơn:</span>
                        <span class="detail-value" id="detail-invoiceNumber">-</span>
                    </div>
                    <div class="detail-item">
                        <span class="detail-label">Ký hiệu:</span>
                        <span class="detail-value" id="detail-invoiceSymbol">-</span>
                    </div>
                    <div class="detail-item">
                        <span class="detail-label">Ngày lập:</span>
                        <span class="detail-value" id="detail-issueDate">-</span>
                    </div>
                </div>

                <!-- Link Result Section -->
                <div id="link-section-misa" class="link-area">
                    <h3>📎 Link PDF Hóa Đơn Đã Được Tạo</h3>
                    
                    <div class="action-buttons">
                        <input type="text" id="pdf-link-misa" class="link-box" readonly>
                        <button class="copy-btn" onclick="copyLinkMisa()">
                            <span>📋 Copy Link</span>
                        </button>
                    </div>

                    <a href="#" id="open-link-misa" class="open-btn" target="_blank">
                        <span>🌐 Mở Link Trong Tab Mới</span>
                    </a>
                </div>

                <div class="instructions">
                    <h4>📝 Hướng Dẫn Sử Dụng Module MISA</h4>
                    <ol>
                        <li><strong>Upload file XML</strong> hóa đơn bằng cách kéo thả hoặc click để chọn file</li>
                        <li>Click <strong>"Phân Tích XML & Tạo Link PDF"</strong> để xử lý file</li>
                        <li>Xem <strong>thông tin hóa đơn</strong> đã được nhận dạng</li>
                        <li><strong>Copy link PDF</strong> được tạo ra và paste vào trình duyệt</li>
                        <li>Hoặc click <strong>"Mở Link Trong Tab Mới"</strong> để tải PDF trực tiếp</li>
                    </ol>
                </div>
            </div>
            
            <!-- Tab 3: Module BKAV -->
            <div class="tab-content" id="module-bkav">
                <div class="module-bkav">
                    <h3>MODULE TẠO LINK TẢI PDF</h3>
                    <p>Module BKAV - Phiên bản chính thức</p>
                    <div class="module-info">
                        <strong>Mã nhận dạng module:</strong> 0101360697
                    </div>
                </div>

                <!-- Phần upload file -->
                <div class="file-upload-bkav">
                    <div class="file-upload-bkav-input" onclick="document.getElementById('fileInputBkav').click()">
                        📁 TẢI LÊN FILE XML (Click hoặc kéo thả file vào đây)
                    </div>
                    <input type="file" id="fileInputBkav" accept=".xml" onchange="handleFileUploadBkav(this.files)">
                </div>

                <div class="xml-input-container">
                    <label class="input-label">
                        <span class="status-indicator status-ready"></span>
                        NỘI DUNG FILE XML:
                    </label>
                    <textarea class="xml-input" id="xmlInputBkav" placeholder="Dán nội dung file XML vào đây hoặc tải lên file..." oninput="validateXMLBkav()"></textarea>
                </div>

                <!-- Thông báo xác thực -->
                <div id="validationMessageBkav" class="validation-message" style="display: none;"></div>

                <div class="button-group">
                    <button class="btn btn-primary" id="generateBtnBkav" onclick="generatePDFLinkBkav()" disabled>
                        🚀 Tạo Link Tải PDF
                    </button>
                    <button class="btn btn-secondary" onclick="loadSampleXMLBkav()">
                        📋 Load XML Mẫu
                    </button>
                    <button class="btn btn-danger" onclick="clearInputBkav()">
                        🗑️ Xóa Dữ Liệu
                    </button>
                </div>

                <div class="bkav-result-container" id="resultContainerBkav">
                    <h3 class="result-title">
                        <span>📎 LINK TẢI FILE PDF</span>
                    </h3>
                    <a href="#" class="bkav-download-link" id="downloadLinkBkav" target="_blank"></a>
                    
                    <div class="button-group">
                        <button class="btn btn-success" onclick="copyToClipboardBkav()">
                            📋 Sao Chép Link
                        </button>
                        <button class="btn btn-primary" onclick="testDownloadLinkBkav()">
                            🔗 Kiểm Tra Link
                        </button>
                    </div>

                    <div class="bkav-link-info" id="linkInfoBkav">
                        <!-- Thông tin link sẽ được hiển thị here -->
                    </div>
                </div>

                <div class="instructions">
                    <h4>📝 Hướng Dẫn Sử Dụng Module BKAV</h4>
                    <ol>
                        <li><strong>Upload file XML</strong> hóa đơn hoặc dán nội dung XML vào ô trên</li>
                        <li>Hệ thống sẽ <strong>tự động xác thực MSTTCGP</strong> trong XML</li>
                        <li>Chỉ khi MSTTCGP trùng với module (0101360697) mới có thể tạo link</li>
                        <li>Click <strong>"Tạo Link Tải PDF"</strong> để tạo link tải PDF từ BKAV</li>
                        <li><strong>Copy link</strong> và dán vào trình duyệt để tải PDF</li>
                    </ol>
                </div>
            </div>
            
            <!-- Tab 4: Tra cứu hóa đơn -->
            <div class="tab-content" id="invoice-lookup">
                <div class="upload-section">
                    <h3>🔍 Tra Cứu Hóa Đơn</h3>
                    <p>Tìm kiếm và tải hóa đơn PDF từ các nhà cung cấp dịch vụ</p>
                    
                    <div class="instructions">
                        <h4>📝 Hướng Dẫn Tra Cứu Hóa Đơn</h4>
                        <ol>
                            <li><strong>Chọn nhà cung cấp</strong> dịch vụ hóa đơn điện tử</li>
                            <li>Nhập <strong>mã tra cứu</strong> nếu có (tìm trong file XML)</li>
                            <li>Click <strong>"Tra cứu"</strong> để tìm hóa đơn</li>
                            <li>Hệ thống sẽ hiển thị các phương án tải PDF phù hợp</li>
                            <li>Chọn phương án tải PDF phù hợp với nhà cung cấp</li>
                        </ol>
                    </div>
                    
                    <div style="text-align: center; margin-top: 30px;">
                        <button class="btn btn-primary" id="openLookupModalBtn">🔍 Mở Công Cụ Tra Cứu</button>
                    </div>
                </div>
            </div>
            
            <!-- Tab 5: Hóa đơn GTGT -->
            <div class="tab-content" id="invoice-gtgt">
                <div class="upload-section">
                    <h3>Tải lên file XML hóa đơn</h3>
                    <p>Sử dụng mẫu hóa đơn GTGT chuyên nghiệp với font Times New Roman và lề 1.5cm</p>
                    <div class="file-input-wrapper">
                        <input type="file" id="xmlFileGtgt" accept=".xml" style="display: none;">
                        <label for="xmlFileGtgt" class="file-input-label">
                            <span>📁 Chọn file XML</span>
                        </label>
                    </div>
                    <p id="fileNameGtgt" style="margin: 15px 0; color: #666;"></p>
                    <button id="uploadBtnGtgt" class="btn btn-primary">Tải lên và Xem trước</button>
                    <button id="requestOriginalBtn" class="btn btn-warning" style="display: none;">📋 Yêu cầu hóa đơn gốc</button>
                </div>
                
                <div class="loading" id="loadingGtgt">
                    <div class="spinner"></div>
                    <p>Đang xử lý file XML...</p>
                </div>
                
                <div id="invoicePreviewGtgt" class="invoice-preview">
                    <div class="pagination-controls">
                        <button id="prevPageBtn" class="pagination-btn" disabled>Trang trước</button>
                        <span class="page-info" id="pageInfo">Trang 1 / 1</span>
                        <button id="nextPageBtn" class="pagination-btn" disabled>Trang tiếp</button>
                    </div>
                    
                    <div class="preview-content" id="previewContentGtgt"></div>
                    
                    <div style="text-align: center; margin-top: 30px;">
                        <button id="showModalBtnGtgt" class="btn btn-warning">👁️ Xem Hóa Đơn Đầy Đủ</button>
                        <button id="downloadPdfGtgt" class="btn btn-success">📥 Tải về PDF</button>
                    </div>
                </div>
                
                <!-- Processing Status for PDF Generation -->
                <div id="pdfProcessingStatus" class="processing-status" style="display: none;">
                    <h4>Đang tạo PDF...</h4>
                    <div class="progress-container">
                        <div class="progress-bar" id="pdfProgressBar">0%</div>
                    </div>
                    <p id="pdfStatusMessage">Đang xử lý...</p>
                </div>
            </div>
        </div>

        <!-- Footer hiển thị MSTTCGP -->
        <div class="msttcgp-footer">
            <h4>📋 DANH SÁCH MÃ SỐ THUẾ TỔ CHỨC CUNG CẤP DỊCH VỤ HÓA ĐƠN ĐIỆN TỬ</h4>
            <div class="msttcgp-list" id="msttcgpList">
                <!-- Danh sách MSTTCGP sẽ được thêm bằng JavaScript -->
            </div>
            <!-- Vùng hiển thị link tra cứu -->
            <div id="footerLookupInfo" style="margin-top: 20px; padding: 15px; background: rgba(255,255,255,0.1); border-radius: 8px;">
                <h5>🔗 Link Tra Cứu Hóa Đơn</h5>
                <p id="footerLookupLink">Chưa có link tra cứu. Hãy sử dụng công cụ tra cứu để tạo link.</p>
            </div>
        </div>
    </div>

    <!-- Modal Hiển Thị Hóa Đơn GTGT -->
    <div class="modal-overlay" id="invoiceModalGtgt">
        <div class="modal-content">
            <div class="modal-header">
                <h2>Hóa Đơn Giá Trị Gia Tăng</h2>
                <button class="close-btn" id="closeModalBtnGtgt">&times;</button>
            </div>
            <div class="modal-body">
                <div id="modalInvoiceContentGtgt"></div>
            </div>
            <div class="modal-footer">
                <div>
                    <button id="printInvoiceBtnGtgt" class="btn btn-primary">🖨️ In Hóa Đơn</button>
                    <button id="downloadModalPdfBtnGtgt" class="btn btn-success">📥 Tải PDF</button>
                </div>
                <button id="closeModalBtn2Gtgt" class="btn">Đóng</button>
            </div>
        </div>
    </div>

    <!-- Modal Tra Cứu Hóa Đơn -->
    <div class="modal-overlay" id="lookupModal">
        <div class="modal-content">
            <div class="modal-header">
                <h2>🔍 Tra Cứu Hóa Đơn</h2>
                <button class="close-btn" id="closeLookupModalBtn">&times;</button>
            </div>
            <div class="modal-body">
                <div class="tabs">
                    <div class="tab active" data-tab="quick-lookup">Tra Cứu Nhanh</div>
                    <div class="tab" data-tab="provider-list">Danh Sách Nhà Cung Cấp</div>
                </div>
                
                <div class="tab-content active" id="quick-lookup">
                    <div class="lookup-form">
                        <div class="form-group">
                            <label for="providerSelect">Chọn nhà cung cấp:</label>
                            <select id="providerSelect" class="form-control">
                                <option value="">-- Chọn nhà cung cấp --</option>
                                <!-- Các nhà cung cấp sẽ được thêm bằng JavaScript -->
                            </select>
                            <div id="providerLoading" style="display: none;">
                                <small>Đang tải thông tin nhà cung cấp... <span class="api-loading"></span></small>
                            </div>
                        </div>
                        
                        <!-- THÔNG TIN HÓA ĐƠN TỪ XML - ĐÃ ĐƯỢC THÊM -->
                        <div id="invoiceInfoFromXml" class="provider-info" style="display: none;">
                            <h4>📋 Thông tin hóa đơn từ XML</h4>
                            <div class="info-grid" id="invoiceInfoDetails">
                                <!-- Thông tin sẽ được thêm bằng JavaScript -->
                            </div>
                        </div>

                        <div id="directPdfInfo" class="direct-pdf-info" style="display: none;">
                            <!-- Thông tin tải PDF trực tiếp sẽ được thêm bằng JavaScript -->
                        </div>

                        <div class="form-group" id="lookupCodeGroup">
                            <label for="lookupCode">Mã tra cứu (nếu có):</label>
                            <input type="text" id="lookupCode" class="form-control" placeholder="Nhập mã tra cứu hóa đơn (không bắt buộc)">
                            <small style="color: #666;">Một số nhà cung cấp không yêu cầu mã tra cứu</small>
                        </div>
                        
                        <button id="performLookupBtn" class="btn btn-primary">🔍 Tra cứu</button>
                        
                        <div id="providerDetails" class="provider-info" style="display: none;">
                            <!-- Thông tin nhà cung cấp sẽ được hiển thị ở đây -->
                        </div>
                        
                        <div id="lookupResult" class="lookup-result">
                            <!-- Kết quả tra cứu sẽ được hiển thị ở đây -->
                        </div>
                        
                        <div id="directLookupSection" class="direct-lookup-section" style="display: none;">
                            <!-- Phần tra cứu trực tiếp sẽ được hiển thị ở đây -->
                        </div>
                    </div>
                </div>
                
                <div class="tab-content" id="provider-list">
                    <div class="lookup-form">
                        <div class="search-box">
                            <input type="text" id="providerSearch" placeholder="🔍 Tìm kiếm nhà cung cấp...">
                        </div>
                        <div id="providerTableContainer">
                            <!-- Bảng danh sách nhà cung cấp sẽ được thêm bằng JavaScript -->
                        </div>
                    </div>
                </div>
            </div>
            <div class="modal-footer">
                <button id="closeLookupModalBtn2" class="btn">Đóng</button>
            </div>
        </div>
    </div>

    <!-- Container ẩn để chụp ảnh -->
    <div id="captureContainer" class="capture-container"></div>

    <script>
        // Biến lưu trữ dữ liệu hóa đơn
        let invoiceData = {};
        let matchedProviders = [];
        let currentSelectedProvider = null;
        
        // Biến lưu trữ link tra cứu hiện tại
        let currentLookupLink = '';
        
        // Module Vĩnh Hy - MSTTCGP 0314743623
        const VIENH_HY_MODULE = {
            mst: "0314743623",
            name: "Công ty TNHH Công nghệ Vĩnh Hy",
            shortName: "Vĩnh Hy",
            apiBaseUrl: "https://ehoadondientu.com",
            pdfGeneratorUrl: "https://ehoadondientu.com/Account/GenerateFile.aspx",
            lookupUrl: "https://ehoadondientu.com/Tra-cuu",
            moduleName: "Vĩnh Hy PDF Generator"
        };

        // Module MISA - MSTTCGP 0101243150
        const MISA_MODULE = {
            mst: "0101243150",
            name: "Công ty Cổ phần MISA (meInvoice)",
            shortName: "MISA",
            apiBaseUrl: "https://meinvoice.vn",
            pdfGeneratorUrl: "https://meinvoice.vn/tra-cuu/DownloadHandler.ashx",
            lookupUrl: "https://meinvoice.vn/tra-cuu/",
            moduleName: "MISA PDF Generator"
        };

        // Module BKAV - MSTTCGP 0101360697
        const BKAV_MODULE = {
            id: "0101360697",
            name: "Công ty Cổ phần BKAV (Bkav eHoadon)",
            shortName: "BKAV",
            baseURL: "https://van.ehoadon.vn/DownloadFile?FilePath=",
            isAuthenticated: false,
            
            // Xác thực MSTTCGP trong XML
            authenticate: function(xmlContent) {
                try {
                    const parser = new DOMParser();
                    const xmlDoc = parser.parseFromString(xmlContent, "text/xml");
                    
                    // Kiểm tra lỗi XML
                    const parseError = xmlDoc.getElementsByTagName("parsererror");
                    if (parseError.length > 0) {
                        return {
                            success: false,
                            error: "XML không hợp lệ: " + parseError[0].textContent
                        };
                    }
                    
                    // Tìm MSTTCGP trong XML
                    const msttcgpElements = xmlDoc.getElementsByTagName("MSTTCGP");
                    let foundMSTTCGP = null;
                    
                    for (let i = 0; i < msttcgpElements.length; i++) {
                        const mstValue = msttcgpElements[i].textContent.trim();
                        if (mstValue) {
                            foundMSTTCGP = mstValue;
                            break;
                        }
                    }
                    
                    if (!foundMSTTCGP) {
                        return {
                            success: false,
                            error: "Không tìm thấy MSTTCGP trong file XML"
                        };
                    }
                    
                    // Kiểm tra trùng khớp với module
                    if (foundMSTTCGP === this.id) {
                        this.isAuthenticated = true;
                        return {
                            success: true,
                            message: "✅ Xác thực thành công! MSTTCGP trùng khớp với module.",
                            msttcgp: foundMSTTCGP
                        };
                    } else {
                        this.isAuthenticated = false;
                        return {
                            success: false,
                            error: `❌ MSTTCGP không trùng khớp! Module: ${this.id}, XML: ${foundMSTTCGP}`
                        };
                    }
                    
                } catch (error) {
                    return {
                        success: false,
                        error: "Lỗi phân tích XML: " + error.message
                    };
                }
            },
            
            // Phân tích XML và trích xuất thông tin
            parseXML: function(xmlContent) {
                if (!this.isAuthenticated) {
                    return {
                        success: false,
                        error: "Chưa được xác thực MSTTCGP"
                    };
                }
                
                try {
                    const parser = new DOMParser();
                    const xmlDoc = parser.parseFromString(xmlContent, "text/xml");
                    
                    // Trích xuất thông tin từ XML
                    const khhDon = xmlDoc.getElementsByTagName("KHHDon")[0]?.textContent || "";
                    const shDon = xmlDoc.getElementsByTagName("SHDon")[0]?.textContent || "";
                    const mcCQT = xmlDoc.getElementsByTagName("MCCQT")[0]?.textContent || "";
                    const msttcgp = xmlDoc.getElementsByTagName("MSTTCGP")[0]?.textContent || "";
                    const nBan = xmlDoc.getElementsByTagName("NBan")[0];
                    const sellerName = nBan?.getElementsByTagName("Ten")[0]?.textContent || "";
                    const sellerMST = nBan?.getElementsByTagName("MST")[0]?.textContent || "";
                    
                    return {
                        success: true,
                        data: {
                            kyyHieu: khhDon,
                            soHoaDon: shDon,
                            maTraCuu: mcCQT,
                            msttcgp: msttcgp,
                            sellerName: sellerName,
                            sellerMST: sellerMST
                        }
                    };
                } catch (error) {
                    return {
                        success: false,
                        error: "Lỗi phân tích XML: " + error.message
                    };
                }
            },
            
            // Tạo đường dẫn file PDF
            generateFilePath: function(data) {
                if (!data.kyyHieu || !data.soHoaDon || !data.maTraCuu) {
                    return null;
                }
                
                // Tạo thư mục con từ ký hiệu (ví dụ: C25TAA -> C2/5T/)
                const folderPath = data.kyyHieu.substring(0, 2) + "/" + 
                                 data.kyyHieu.substring(2, 4) + "/";
                
                // Tạo tên file
                const fileName = `${data.kyyHieu}-${data.soHoaDon}-${data.maTraCuu}-DPH.pdf`;
                
                return folderPath + fileName;
            },
            
            // Tạo link download hoàn chỉnh
            createDownloadLink: function(filePath) {
                if (!filePath) return null;
                return this.baseURL + encodeURIComponent(filePath) + "&BFType=1";
            },
            
            // Tạo link từ XML content
            generateFromXML: function(xmlContent) {
                // Xác thực trước
                const authResult = this.authenticate(xmlContent);
                if (!authResult.success) {
                    return authResult;
                }
                
                const parseResult = this.parseXML(xmlContent);
                if (!parseResult.success) {
                    return parseResult;
                }
                
                const filePath = this.generateFilePath(parseResult.data);
                if (!filePath) {
                    return {
                        success: false,
                        error: "Không thể tạo đường dẫn file từ dữ liệu XML"
                    };
                }
                
                const downloadLink = this.createDownloadLink(filePath);
                
                return {
                    success: true,
                    data: {
                        ...parseResult.data,
                        filePath: filePath,
                        downloadLink: downloadLink
                    }
                };
            }
        };

        // Module EasyInvoice - MSTTCGP 0105987432
        const EASYINVOICE_MODULE = {
            mst: "0105987432",
            name: "Công ty Cổ phần Đầu tư công nghệ và thương mại Softdreams (EasyInvoice)",
            shortName: "EasyInvoice",
            moduleName: "EasyInvoice Generator"
        };

        // Hàm tạo link EasyInvoice (không phải module)
        function createEasyInvoiceLookupURL(jsonData) {
            try {
                // Sử dụng dữ liệu từ JSON thay vì XML
                const xmlContent = jsonData.xmlContent;
                const parser = new DOMParser();
                const xmlDoc = parser.parseFromString(xmlContent, "text/xml");
                
                // Check for XML parsing errors
                if (xmlDoc.getElementsByTagName("parsererror").length > 0) {
                    throw new Error('XML không hợp lệ');
                }
                
                // Extract seller tax code
                const sellerTaxCode = xmlDoc.querySelector('NBan MST')?.textContent || '';
                
                // Extract lookup info from TTKhac
                let portalLink = '';
                let fkey = '';
                
                const infoNodes = xmlDoc.querySelectorAll('TTKhac TTin');
                infoNodes.forEach(node => {
                    const field = node.querySelector('TTruong')?.textContent;
                    const value = node.querySelector('DLieu')?.textContent;
                    
                    if (field === 'PortalLink') portalLink = value || '';
                    if (field === 'Fkey') fkey = value || '';
                });
                
                // If no portal link found, generate from tax code
                if (!portalLink && sellerTaxCode) {
                    portalLink = `https://${sellerTaxCode}hd.easyinvoice.com.vn`;
                }
                
                // Validate required data
                if (!portalLink || !fkey) {
                    throw new Error('Thiếu thông tin tra cứu');
                }
                
                // Extract optional invoice info for better UX
                const invoicePattern = xmlDoc.querySelector('TTChung KHHDon')?.textContent || '';
                const invoiceNo = xmlDoc.querySelector('TTChung SHDon')?.textContent || '';
                
                // Construct URL
                const baseUrl = portalLink.replace(/\/+$/, '');
                let lookupUrl = `${baseUrl}/Search/Index?fkey=${encodeURIComponent(fkey)}`;
                
                // Add optional parameters
                if (invoicePattern) lookupUrl += `&symbol=${encodeURIComponent(invoicePattern)}`;
                if (invoiceNo) lookupUrl += `&invoiceNo=${encodeURIComponent(invoiceNo)}`;
                
                return {
                    success: true,
                    lookupUrl: lookupUrl,
                    portalLink: portalLink,
                    fkey: fkey,
                    sellerTaxCode: sellerTaxCode,
                    invoicePattern: invoicePattern,
                    invoiceNo: invoiceNo
                };
                
            } catch (error) {
                console.error('Lỗi tạo link tra cứu:', error.message);
                return {
                    success: false,
                    error: error.message
                };
            }
        }

        // Cập nhật tên tab với tên module
        document.getElementById('module-tab').textContent = VIENH_HY_MODULE.moduleName;
        document.getElementById('module-misa-tab').textContent = MISA_MODULE.moduleName;
        document.getElementById('module-bkav-tab').textContent = "Module BKAV";
        
        // Danh sách nhà cung cấp dịch vụ hóa đơn điện tử
        const serviceProviders = [
            { mst: "0101243150", name: "Công ty Cổ phần MISA (meInvoice)", url: "https://www.meinvoice.vn/tra-cuu/" },
            { mst: "0314743623", name: "Công ty TNHH Công nghệ Vĩnh Hy", url: "https://ehoadondientu.com/Tra-cuu" },
            { mst: "0101360697", name: "Công ty Cổ phần BKAV (Bkav eHoadon)", url: "https://tchd.ehoadon.vn/TCHD?" },
            { mst: "0104128565", name: "Công ty TNHH Hệ thống thông tin FPT (FPT eInvoice)", url: "https://tracuuhoadon.fpt.com.vn/" },
            { mst: "0102519041", name: "Công ty Cổ phần Công nghệ tin học EFY Việt Nam (iHOADON)", url: "https://ihoadon.vn/kiem-tra/" },
            { mst: "0105987432", name: "Công ty Cổ phần Đầu tư công nghệ và thương mại Softdreams (EasyInvoice)", url: "http://tracuu.easyinvoice.vn/" },
            { mst: "0302999571", name: "Công ty TNHH L.C.S (LCS eInvoice)", url: "https://einvoice.lcs.com.vn" },
            { mst: "0313963672", name: "Công ty TNHH Soft Ware KK VAT", url: "https://hoadon.kkvat.com.vn/ehoadon/ViewIssueBill.jsp" },
            { mst: "0105232093", name: "Công ty Cổ phần CyberLotus", url: "https://tracuu.cyberbill.vn/" },
            { mst: "0311942758", name: "Công ty TNHH Thương Mại Ngô Gia Phát", url: "http://tracuu.ngogiaphat.vn" },
            { mst: "0302712571", name: "Công ty Cổ phần Mắt Bão (Mifi)", url: "https://mifi.vn/tra-cuu-hoa-don/" },
            { mst: "0103930279", name: "Công ty Cổ phần công nghệ thẻ Nacencomm", url: "https://hddt.nacencomm.vn/" },
            { mst: "0105844836", name: "Công ty TNHH Công nghệ LCD Việt Nam (AZinvoice)", url: "https://azinvoice.com" },
            { mst: "0106026495", name: "Công ty TNHH Hóa đơn điện tử M-INVOICE", url: "https://tracuuhoadon.minvoice.com.vn/" },
            { mst: "0313906508", name: "Công ty Cổ phần Phát triển công nghệ Nguyễn Minh", url: "http://tracuu.nguyenminhvat.vn/" },
            { mst: "0101300842", name: "Công ty TNHH Phát triển công nghệ Thái Sơn (E-invoice)", url: "https://einvoice.vn/tra-cuu-hoa-don/" },
            { mst: "0306784030", name: "Công ty TNHH Máy tính và truyền thông công nghệ kết nối (eHoaDon Online)", url: "https://ehoadon.online/einvoice/lookup" },
            { mst: "0200638946", name: "Công ty Cổ phần công nghệ số và in đồ họa (Oinvoice)", url: "https://oinvoice.vn/tra-cuu" },
            { mst: "0312303803", name: "Công ty TNHH Win Tech Solution (WININVOICE)", url: "http://tracuu.wininvoice.vn/" },
            { mst: "0100109106", name: "Tập đoàn Công nghiệp – Viễn thông quân đội (Viettel/S-Invoice)", url: "https://vinvoice.viettel.vn/utilities/invoice-search" },
            { mst: "0102454468", name: "Công ty Cổ phần Công nghệ thông tin Đông Nam Á (Tax24)", url: "https://hoadondientu.tax24.com.vn/Tracuu.aspx" },
            { mst: "0105937449", name: "Công ty Cổ phần hóa đơn điện tử New-Invoice", url: "https://newinvoice.com.vn/tra-cuu/" },
            { mst: "0108516079", name: "Công ty Cổ phần Giải pháp phần mềm 3A", url: "http://tracuu.3asoft.vn/" },
            { mst: "0100686209", name: "Tổng công ty Viễn thông Mobifone (Mobifone Invoice)", url: "https://tracuu.mobifoneinvoice.vn/" },
            { mst: "0100684378", name: "Tập Đoàn Bưu chính viễn thông Việt Nam (VNPT-Invoice)", url: "https://portaltool-miennam.vnpt-invoice.com.vn/" },
            { mst: "0401486901", name: "Công ty Cổ phần thương mại Visnam (VIN-HOADON)", url: "https://tracuu.vin-hoadon.com/" },
            { mst: "0200784873", name: "Công ty Cổ phần Thiết bị điện – Điện tử Bách Khoa", url: "https://hoadonbachkhoa.pmbk.vn/tra-cuu-hoa-don" },
            { mst: "0106713804", name: "Công ty Cổ phần dịch vụ T-VAN HILO", url: "https://tracuuhddt78.hilo.com.vn/" },
            { mst: "0314209362", name: "Công ty Cổ phần Minh Khang Group", url: "https://hoadondientuvat.com/Tracuu.aspx" },
            { mst: "0101352495", name: "Công ty Cổ phần Giải pháp hóa đơn điện tử Việt Nam (VN-Invoice)", url: "http://tracuu.vninvoice.vn/" },
            { mst: "0102182292", name: "Công ty Cổ phần giải pháp thanh toán Việt Nam", url: "https://www.vnpay.vn" },
            { mst: "0106870211", name: "Công ty Cổ phần ICORP (Viet-invoice)", url: "https://viet-invoice.vn/TraCuu/" },
            { mst: "0104614692", name: "Công ty Cổ phần đầu tư và công nghệ idocNet (Qinvoice)", url: "https://hoadontvan.com/TraCuu" },
            { mst: "0309612872", name: "Công ty Cổ phần chữ ký số VI NA", url: "www.smartsign.com.vn" },
            { mst: "0309478306", name: "Công ty Cổ phần TS24", url: "http://www.hoadondientu.ts24.com.vn/Tracuuhoadon/" },
            { mst: "0315298333", name: "Công ty TNHH Hóa đơn điện tử TCT (TCTINVOICE)", url: "http://tracuu.tctinvoice.vn/" },
            { mst: "0303609305", name: "Công ty TNHH Tin học Tia lửa Việt (ihoadondientu)", url: "http://tracuu.ihoadondientu.com/" },
            { mst: "0100727825", name: "Công ty Cổ phần phần mềm quản lý doanh nghiệp (Fast)", url: "https://invoice.fast.com.vn/" },
            { mst: "0315467091", name: "Công ty TNHH ACCONLINE.VN", url: "https://acconline.vn/vn/tra-cuu-hoa-don.htm" },
            { mst: "0315638251", name: "Công ty Cổ phần công nghệ hóa đơn điện tử HT", url: "https://tracuu.htinvoice.vn/" },
            { mst: "0105958921", name: "Công ty Cổ phần công nghệ ITT (CloudInvoice)", url: "http://tracuu.cloudinvoice.vn/" },
            { mst: "0302431595", name: "Công ty TNHH PA Việt Nam (Hóa đơn 30S)", url: "https://tracuu.hoadon30s.vn/" },
            { mst: "0103018807", name: "Công ty Cổ phần tích hợp công nghệ VNISC", url: "https://hoadondientu.vninvoice.vn" },
            { mst: "0106820789", name: "Công ty TNHH Giải pháp hóa đơn điện tử My – Invoice", url: "http://tracuu.my-invoice.vn/" },
            { mst: "0310151055", name: "Công ty Cổ phần Chứng số An toàn (SAFE-Invoice)", url: "http://tracuuhoadon.safe-invoice.vn/" },
            { mst: "0303430876", name: "Công ty Cổ phần công nghệ San Phú", url: "www.spc-technology.com" },
            { mst: "0301452923", name: "Công ty TNHH Giấy vi tính Liên Sơn", url: "https://hoadondientu.lienson.vn/tracuuhoadon.aspx" },
            { mst: "0314185087", name: "Công ty TNHH Thương Mại dịch vụ Online VI NA", url: "https://checkinvoice.onlinevina.com.vn/" },
            { mst: "0400462489", name: "Công ty TNHH Tuần Châu", url: "http://e-invoicetuanchau.com/Invoice/Search" },
            { mst: "03500456910", name: "Công ty TNHH Minh Thư", url: "https://hoadonminhthuvungtau.com" },
            { mst: "0104908371", name: "Công ty Cổ phần phát triển công nghệ ACMAN", url: "https://www.acman.vn/hoa-don-dien-tu.html" },
            { mst: "0315191291", name: "Công ty TNHH Tư vấn thương mại Trí Việt Luật", url: "https://hoadonsovn.net" },
            { mst: "0313844107", name: "Công ty TNHH Đầu tư Hòn Ngọc Việt", url: "http://voice.hoadondientu.net.vn" },
            { mst: "0311622035", name: "Công ty TNHH Dịch vụ Trí Việt Luật", url: "http://congtyinhoadon.com" },
            { mst: "0106361479", name: "Công ty Cổ phần truyền số liệu Việt Nam (aHoadon)", url: "https://tracuu.ahoadon.com/" },
            { mst: "0312270160", name: "Công ty TNHH NC9 Việt Nam", url: "http://ameinvoice.com.vn" },
            { mst: "0104493085", name: "Công ty Cổ phần giải pháp First Trust", url: "https://www.fts.com.vn/phan-mem-hoa-don-dien-tu/" },
            { mst: "0101289966", name: "Công ty TNHH Phần mềm Nhân Hòa", url: "https://hoadon.biz" },
            { mst: "0303211948", name: "Công ty TNHH Kế toán và tư vấn V.L.C", url: "https://ketoanvlc.com" },
            { mst: "0101622374", name: "Công ty Cổ phần Công nghệ và giải pháp Tâm Việt", url: "http://tamvietgroup.vn" },
            { mst: "0310768095", name: "Công ty TNHH Dịch vụ phần mềm AVSE", url: "http://hoadondientu.link" },
            { mst: "0312961577", name: "Công ty TNHH MTV in Bến Thành", url: "http://tracuuhoadon.benthanhvoice.vn" },
            { mst: "0313950909", name: "Công ty TNHH ZAMO (Koffi)", url: "https://koffi.vn/outbound/lookup-invoice" },
            { mst: "0311928954", name: "Công ty Cổ phần công nghệ VIETINFO", url: "https://tracuu.vietinfo.tech/" },
            { mst: "0103770970", name: "Công ty Cổ phần phát triển phần mềm và công nghệ Bitware", url: "https://www.bitware.vn/hoadondientu" },
            { mst: "0305142231", name: "Công ty Cổ phần phần mềm Rosy", url: "https://einv.rosysoft.vn:8386/RSeInvoiceSearch" },
            { mst: "03702037020", name: "Công ty TNHH MTV thương mại dịch vụ Trần Đình Tùng", url: "https://trandinhtung.evat.vn" },
            { mst: "0101925883", name: "Công ty TNHH Tổng công ty Công nghệ và Giải pháp CMC (CMC TS)", url: "http://tracuu.cmcsoft.com/" },
            { mst: "0316642395", name: "Công ty TNHH Công nghệ và tư vấn Phương Nam", url: "https://phuongnam.evat.vn/log" },
            { mst: "0315194912", name: "Công ty TNHH Dịch vụ kế toán – Tư vấn thuế TTL", url: "https://ttltax.com" },
            { mst: "0315983667", name: "Công ty Cô phần Công nghệ Phát triển hóa đơn điện tử Việt Nam", url: "http://hoadondientuvietnam.vn" },
            { mst: "0310926922", name: "Công ty TNHH Phần mềm kế toán và dịch vụ thủ tục thuế Sài Gòn", url: "https://invoice.ehcm.vn" },
            { mst: "0101010702", name: "Công ty Cổ phần Phần mềm Thăng Long", url: "http://thanglongsoft.com/" },
            { mst: "0102720409", name: "Công ty Cổ phần Hóa đơn điện tử TIG Thăng Long", url: "http://trahoadon.tigtax.vn/" },
            { mst: "0314058603", name: "Công ty TNHH Viễn thông Đông Sài Gòn", url: "https://tracuu.vdsg-invoice.vn/" },
            { mst: "0301448733", name: "Công ty Cổ phần Tin học Lạc Việt", url: "http://tracuu.accnet.vn/" },
            { mst: "0313253288", name: "Công ty Cổ phần Công nghệ TADU", url: "https://autoinvoice.vn" },
            { mst: "0309889835", name: "Công ty Cổ phần Công nghệ UNIT", url: "https://unit.com.vn/" },
            { mst: "0202029650", name: "Công ty Cổ phần phát triển và ứng dụng phần mềm Bách Khoa", url: "https://hoadondientu.pmbk.vn" },
            { mst: "0108971656", name: "Công ty Cổ phần My Software", url: "https://tracuu.myinvoice.com.vn/" },
            { mst: "0312942260", name: "Công ty TNHH Công nghệ HT Sài Gòn", url: "http://tracuu.ihoadondientu.net/" },
            { mst: "01201496252", name: "Công ty Cổ phần WEBCASH Việt Nam", url: "https://einvoice.webcashvietnam.com/" },
            { mst: "0303549303", name: "Công ty TNHH Phần mềm và Tư vấn Kim Tự Tháp", url: "https://e-invoices.vn/" },
            { mst: "0311914694", name: "Công ty TNHH Phần mềm BRB", url: "http://brightbrain.vn/hoa-don-dien-tu/" },
            { mst: "0312617990", name: "Công ty TNHH Nhóm Mây", url: "https://www.cloudteam.vn" },
            { mst: "0109282176", name: "Công ty Cổ phần Hóa đơn điện tử VININVOICE", url: "https://vininvoice.vn/TraCuu" },
            { mst: "0102723181", name: "Trung tâm Tin học và Công nghệ số", url: "http://hoadonct.gov.vn" },
            { mst: "0106858609", name: "Công ty Cổ phần VETC", url: "https://vetc.com.vn" },
            { mst: "0315151651", name: "Công ty TNHH Phần mềm PVS", url: "https://pvssolution.com/" },
            { mst: "0310151739", name: "Công ty Cổ phần Dịch vụ Thương mại Việt Nam trực tuyến", url: "https://news.yoinvoice.vn" },
            { mst: "0312575123", name: "Công ty TNHH Ecount Việt Nam", url: "https://ecount.com" },
            { mst: "0107732197", name: "Công ty Cổ phần ATIS", url: "https://tracuu.atis.com.vn/" },
            { mst: "0101659906", name: "Công ty Cổ phần GMO-Z.com RUNSYSTEM (KAIKE)", url: "https://einvoice.kaike.vn/tracuu" },
            { mst: "0103019524", name: "Công ty Cổ phần Tin học – Viễn thông Hàng không (AITS)", url: "https://tracuu.aits.vn/" },
            { mst: "0316114998", name: "Công ty TNHH Bizzi VietNam", url: "https://bizzi.vn/" },
            { mst: "0316636497", name: "Công ty Cổ phần Công nghệ BEE (Bee Logistics)", url: "https://e-invoice.beelogistics.com/" },
            { mst: "0106249501", name: "Công ty Cổ phần MONT-E", url: "http://tracuuhoadon.mont-e.com/" },
            { mst: "0201802839", name: "Công ty TNHH Tư vấn và Dịch vụ Home Casta", url: "https://homecasta.vn" },
            { mst: "04601328480", name: "Công ty CP Tư vấn và Chuyển giao công nghệ Sơn Phát", url: "https://sonphat.vn" }
        ];

        // Danh sách nhà cung cấp hỗ trợ tải PDF trực tiếp
        const directPdfDownloadProviders = [
            {
                mst: "0101243150",
                name: "Công ty Cổ phần MISA (meInvoice)",
                pdfUrlPattern: "https://meinvoice.vn/tra-cuu/DownloadHandler.ashx?Type=pdf&MST={sellerTaxCode}&Code={transactionId}&Module={moduleCode}",
                description: "Hỗ trợ tải PDF trực tiếp từ MISA",
                requiresVerificationCode: false,
                autoFillSupported: true
            },
            {
                mst: "0100684378", 
                name: "Tập Đoàn Bưu chính viễn thông Việt Nam (VNPT-Invoice)",
                pdfUrlPattern: "https://portaltool-miennam.vnpt-invoice.com.vn/api/invoice/download?code={code}",
                description: "Hỗ trợ tải PDF trực tiếp từ VNPT",
                requiresVerificationCode: true,
                autoFillSupported: false
            },
            {
                mst: "0100109106",
                name: "Tập đoàn Công nghiệp – Viễn thông quân đội (Viettel/S-Invoice)",
                pdfUrlPattern: "https://vinvoice.viettel.vn/api/invoice/download/{code}",
                description: "Hỗ trợ tải PDF trực tiếp từ Viettel",
                requiresVerificationCode: true,
                autoFillSupported: false
            },
            {
                mst: "0101360697",
                name: "Công ty Cổ phần BKAV (Bkav eHoadon)",
                pdfUrlPattern: "https://van.ehoadon.vn/DownloadFile?FilePath={filePath}&BFType=1",
                description: "Hỗ trợ tải PDF trực tiếp từ BKAV",
                requiresVerificationCode: false,
                autoFillSupported: true
            },
            {
                mst: "0314743623",
                name: "Công ty TNHH Công nghệ Vĩnh Hy",
                pdfUrlPattern: "https://ehoadondientu.com/api/download/{code}",
                description: "Hỗ trợ tải PDF trực tiếp từ Vĩnh Hy",
                requiresVerificationCode: false,
                autoFillSupported: true
            },
            {
                mst: "0104128565",
                name: "Công ty TNHH Hệ thống thông tin FPT (FPT eInvoice)",
                pdfUrlPattern: "https://tracuuhoadon.fpt.com.vn/api/download/{code}",
                description: "Hỗ trợ tải PDF trực tiếp từ FPT",
                requiresVerificationCode: false,
                autoFillSupported: true
            },
            {
                mst: "0105987432",
                name: "Công ty Cổ phần Đầu tư công nghệ và thương mại Softdreams (EasyInvoice)",
                pdfUrlPattern: "{portalLink}/Search/Index?fkey={fkey}",
                description: "Hỗ trợ tra cứu trực tiếp từ EasyInvoice",
                requiresVerificationCode: false,
                autoFillSupported: true
            }
        ];

        // Hàm tạo URL tra cứu đặc biệt cho các nhà cung cấp cụ thể
        function createSpecialLookupUrl(providerMST, transactionID, sellerTaxCode, verificationCode) {
            if (!transactionID) return null;
            
            switch(providerMST) {
                case "0101360697": // BKAV
                    return `https://van.ehoadon.vn/Lookup?InvoiceGUID=${transactionID}`;
                case "0101243150": // MISA
                    return `https://www.meinvoice.vn/tra-cuu/?sc=${transactionID}`;
                case "0100109106": // Viettel
                    let viettelUrl = "https://vinvoice.viettel.vn/utilities/invoice-search";
                    if (sellerTaxCode && verificationCode) {
                        viettelUrl += `?sellerTaxCode=${encodeURIComponent(sellerTaxCode)}&verificationCode=${encodeURIComponent(verificationCode)}`;
                    }
                    return viettelUrl;
                case "0100684378": // VNPT
                    let vnptUrl = "https://portaltool-miennam.vnpt-invoice.com.vn/";
                    if (verificationCode) {
                        vnptUrl += `search?code=${encodeURIComponent(verificationCode)}`;
                    }
                    return vnptUrl;
                case "0314743623": // Vĩnh Hy
                    let vinhHyUrl = "https://ehoadondientu.com/Tra-cuu";
                    if (sellerTaxCode && verificationCode) {
                        vinhHyUrl += `?seller=${encodeURIComponent(sellerTaxCode)}&code=${encodeURIComponent(verificationCode)}`;
                    }
                    return vinhHyUrl;
                case "0104128565": // FPT
                    let fptUrl = "https://tracuuhoadon.fpt.com.vn/";
                    if (sellerTaxCode) {
                        fptUrl += `search?taxCode=${encodeURIComponent(sellerTaxCode)}`;
                    }
                    return fptUrl;
                case "0105987432": // EasyInvoice
                    let easyInvoiceUrl = "http://tracuu.easyinvoice.vn/";
                    if (sellerTaxCode) {
                        easyInvoiceUrl = `https://${sellerTaxCode}hd.easyinvoice.com.vn/Search/Index`;
                    }
                    return easyInvoiceUrl;
                default:
                    return null;
            }
        }

        // Hàm tạo URL tự động điền thông tin
        function createAutoFillUrl(providerMST, sellerTaxCode, verificationCode, transactionID) {
            if (!providerMST || !sellerTaxCode) return null;
            
            const provider = serviceProviders.find(p => p.mst === providerMST);
            if (!provider) return null;
            
            let baseUrl = provider.url;
            
            // Thêm tham số tự động điền cho các nhà cung cấp hỗ trợ
            const params = new URLSearchParams();
            
            if (sellerTaxCode) {
                params.append('sellerTaxCode', sellerTaxCode);
                params.append('taxCode', sellerTaxCode);
                params.append('mst', sellerTaxCode);
            }
            
            if (verificationCode) {
                params.append('verificationCode', verificationCode);
                params.append('code', verificationCode);
                params.append('fkey', verificationCode);
                params.append('searchCode', verificationCode);
            }
            
            if (transactionID) {
                params.append('transactionId', transactionID);
                params.append('invoiceId', transactionID);
                params.append('guid', transactionID);
            }
            
            const queryString = params.toString();
            return queryString ? `${baseUrl}${baseUrl.includes('?') ? '&' : '?'}${queryString}` : baseUrl;
        }

        // Lấy thông tin nhà cung cấp dịch vụ từ API
        async function getServiceProviderInfo(taxCode) {
            if (!taxCode) return null;
            
            try {
                const response = await fetch(`https://api.vietqr.io/v2/business/${taxCode}`);
                if (!response.ok) {
                    throw new Error(`HTTP error! status: ${response.status}`);
                }
                const data = await response.json();
                return data;
            } catch (error) {
                console.error('Lỗi khi lấy thông tin nhà cung cấp dịch vụ:', error);
                return null;
            }
        }

        // Khởi tạo danh sách nhà cung cấp
        function initializeProviders() {
            const providerSelect = document.getElementById('providerSelect');
            providerSelect.innerHTML = '<option value="">-- Chọn nhà cung cấp --</option>';
            
            // Nếu có nhà cung cấp phù hợp, chỉ hiển thị những nhà cung cấp đó
            if (matchedProviders.length > 0) {
                matchedProviders.forEach(provider => {
                    const option = document.createElement('option');
                    option.value = provider.mst;
                    option.textContent = `${provider.name} (MST: ${provider.mst})`;
                    option.setAttribute('data-provider-name', provider.name);
                    option.setAttribute('data-mst', provider.mst);
                    option.setAttribute('data-url', provider.url);
                    providerSelect.appendChild(option);
                });
            } else {
                // Nếu không có nhà cung cấp phù hợp, hiển thị tất cả
                serviceProviders.forEach(provider => {
                    const option = document.createElement('option');
                    option.value = provider.mst;
                    option.textContent = `${provider.name} (MST: ${provider.mst})`;
                    option.setAttribute('data-provider-name', provider.name);
                    option.setAttribute('data-mst', provider.mst);
                    option.setAttribute('data-url', provider.url);
                    providerSelect.appendChild(option);
                });
            }
        }

        // Tìm nhà cung cấp phù hợp dựa trên MSTTCGP
        function findMatchingProviders(taxCode) {
            if (!taxCode) return [];
            
            const matched = [];
            
            // Tìm kiếm chính xác
            serviceProviders.forEach(provider => {
                if (provider.mst === taxCode) {
                    matched.push(provider);
                }
            });
            
            // Nếu không tìm thấy kết quả chính xác, tìm kiếm gần đúng
            if (matched.length === 0) {
                serviceProviders.forEach(provider => {
                    if (provider.mst.includes(taxCode) || taxCode.includes(provider.mst)) {
                        matched.push(provider);
                    }
                });
            }
            
            return matched;
        }

        // Kiểm tra nhà cung cấp có hỗ trợ tải PDF trực tiếp không
        function checkDirectPdfDownloadSupport(taxCode, verificationCode) {
            if (!taxCode) return null;
            
            // Tìm nhà cung cấp trong danh sách hỗ trợ tải PDF
            const provider = directPdfDownloadProviders.find(p => p.mst === taxCode);
            
            if (provider) {
                // Tạo URL tải PDF
                let pdfUrl = provider.pdfUrlPattern;
                
                // Thay thế các tham số
                pdfUrl = pdfUrl.replace('{sellerTaxCode}', encodeURIComponent(invoiceData.sellerTaxCode || ''));
                pdfUrl = pdfUrl.replace('{transactionId}', encodeURIComponent(invoiceData.transactionID || ''));
                pdfUrl = pdfUrl.replace('{moduleCode}', encodeURIComponent(provider.mst));
                
                // Nếu cần mã tra cứu và có mã, thay thế vào URL
                if (provider.requiresVerificationCode && verificationCode) {
                    pdfUrl = pdfUrl.replace('{code}', encodeURIComponent(verificationCode));
                } else if (!provider.requiresVerificationCode) {
                    // Nếu không cần mã tra cứu, có thể sử dụng URL gốc
                    pdfUrl = pdfUrl.replace('{code}', '');
                } else {
                    // Nếu cần mã tra cứu nhưng không có mã, không thể tạo URL
                    return {
                        provider: provider,
                        pdfUrl: null,
                        requiresCode: true
                    };
                }
                
                return {
                    provider: provider,
                    pdfUrl: pdfUrl,
                    requiresCode: provider.requiresVerificationCode,
                    autoFillSupported: provider.autoFillSupported
                };
            }
            
            return null;
        }

        // Hiển thị phần tải PDF thông minh
        function showSmartDownloadSection() {
            const smartSection = document.getElementById('smartDownloadSection');
            const pdfDownloadInfo = checkDirectPdfDownloadSupport(
                invoiceData.serviceProviderTaxCode, 
                invoiceData.verificationCode
            );
            
            let sectionContent = '<h4>📥 Tải hóa đơn PDF</h4>';
            
            // Phương án 1: Tải trực tiếp từ nhà cung cấp (nếu được hỗ trợ)
            if (pdfDownloadInfo && pdfDownloadInfo.pdfUrl) {
                sectionContent += `
                    <div class="download-option">
                        <h5>Tải trực tiếp từ nhà cung cấp <span class="option-badge">Khuyến nghị</span></h5>
                        <p>Nhà cung cấp <strong>${pdfDownloadInfo.provider.name}</strong> hỗ trợ tải PDF trực tiếp.</p>
                        <p>${pdfDownloadInfo.provider.description}</p>
                        <div style="margin-top: 15px;">
                            <a href="${pdfDownloadInfo.pdfUrl}" target="_blank" class="pdf-download-btn">
                                📥 Tải PDF trực tiếp
                            </a>
                            <p style="margin-top: 10px; font-size: 12px; color: #666;">
                                <em>Liên kết này sẽ mở trong tab mới</em>
                            </p>
                        </div>
                    </div>
                `;
            } else if (pdfDownloadInfo && pdfDownloadInfo.requiresCode && !invoiceData.verificationCode) {
                // Nhà cung cấp hỗ trợ nhưng cần mã tra cứu mà không có
                sectionContent += `
                    <div class="download-option">
                        <h5>Tải từ nhà cung cấp <span class="option-badge option-warning">Cần mã tra cứu</span></h5>
                        <p>Nhà cung cấp <strong>${pdfDownloadInfo.provider.name}</strong> hỗ trợ tải PDF trực tiếp nhưng yêu cầu mã tra cứu.</p>
                        <p><strong>Khuyến nghị:</strong> Sử dụng chức năng "Tra cứu hóa đơn" để lấy mã tra cứu hoặc tải PDF từ XML bên dưới.</p>
                    </div>
                `;
            }
            
            // Phương án 2: Tạo PDF từ XML (luôn khả dụng)
            sectionContent += `
                <div class="download-option">
                    <h5>Tạo PDF từ XML <span class="option-badge">Luôn khả dụng</span></h5>
                    <p>Tạo file PDF từ dữ liệu XML của hóa đơn. Phương pháp này luôn hoạt động nhưng có thể không giống 100% với bản gốc.</p>
                    <div style="margin-top: 15px;">
                        <button id="generatePdfFromXml" class="pdf-download-btn" style="border: none; cursor: pointer;">
                            🖨️ Tạo PDF từ XML
                        </button>
                    </div>
                </div>
            `;
            
            // Phương án 3: Khuyến nghị tra cứu (nếu không có mã)
            if (!invoiceData.verificationCode) {
                sectionContent += `
                    <div class="pdf-recommendation">
                        <h5>💡 Khuyến nghị</h5>
                        <p>Không tìm thấy mã tra cứu trong file XML. Bạn có thể:</p>
                        <ul style="margin: 10px 0; padding-left: 20px;">
                            <li>Sử dụng chức năng "Tra cứu hóa đơn" để tìm mã tra cứu</li>
                            <li>Liên hệ nhà cung cấp dịch vụ để được hỗ trợ</li>
                            <li>Tạo PDF từ XML (phương pháp thay thế)</li>
                        </ul>
                    </div>
                `;
            }
            
            smartSection.innerHTML = sectionContent;
            smartSection.style.display = 'block';
            
            // Thêm sự kiện cho nút tạo PDF từ XML
            document.getElementById('generatePdfFromXml').addEventListener('click', function() {
                captureAndDownloadPdf();
            });
        }

        // Cập nhật danh sách nhà cung cấp với thông tin từ API
        async function updateProvidersWithApiInfo(serviceTaxCode) {
            if (!serviceTaxCode) return;
            
            const providerSelect = document.getElementById('providerSelect');
            const providerLoading = document.getElementById('providerLoading');
            
            // Hiển thị loading
            providerLoading.style.display = 'block';
            
            try {
                const providerInfo = await getServiceProviderInfo(serviceTaxCode);
                
                if (providerInfo && providerInfo.data) {
                    serviceProviderInfo = providerInfo.data;
                    
                    // Tìm và cập nhật option tương ứng trong dropdown
                    const options = providerSelect.getElementsByTagName('option');
                    for (let option of options) {
                        const mst = option.getAttribute('data-mst');
                        if (mst === serviceTaxCode) {
                            // Cập nhật tên hiển thị với thông tin từ API
                            const apiName = providerInfo.data.name || option.getAttribute('data-provider-name');
                            option.textContent = `${apiName} (MST: ${serviceTaxCode})`;
                            option.selected = true;
                            break;
                        }
                    }
                    
                    // Cập nhật thông tin chi tiết nhà cung cấp
                    updateProviderDetails(providerInfo.data);
                }
            } catch (error) {
                console.error('Lỗi khi cập nhật thông tin nhà cung cấp:', error);
            } finally {
                providerLoading.style.display = 'none';
            }
        }

        // Cập nhật thông tin chi tiết nhà cung cấp
        function updateProviderDetails(providerData) {
            const providerDetails = document.getElementById('providerDetails');
            
            if (providerData) {
                providerDetails.innerHTML = `
                    <h4>Thông tin nhà cung cấp dịch vụ hóa đơn điện tử:</h4>
                    <p><strong>Tên đơn vị:</strong> ${providerData.name || 'Không có thông tin'}</p>
                    <p><strong>Mã số thuế:</strong> ${providerData.taxCode || 'Không có thông tin'}</p>
                    <p><strong>Địa chỉ:</strong> ${providerData.address || 'Không có thông tin'}</p>
                    <p><strong>Người đại diện:</strong> ${providerData.representative || 'Không có thông tin'}</p>
                    <p><strong>Trạng thái:</strong> ${providerData.status || 'Không có thông tin'}</p>
                `;
                providerDetails.style.display = 'block';
            }
        }

        // Hiển thị danh sách nhà cung cấp trong bảng
        function displayProviderList() {
            const container = document.getElementById('providerTableContainer');
            
            let tableHTML = `
                <table class="provider-table">
                    <thead>
                        <tr>
                            <th>STT</th>
                            <th>MSTTCGP</th>
                            <th>Tên Nhà Cung Cấp</th>
                            <th>Link Tra Cứu</th>
                            <th>Hỗ trợ PDF</th>
                            <th>Tự động điền</th>
                        </tr>
                    </thead>
                    <tbody>
            `;
            
            serviceProviders.forEach((provider, index) => {
                const supportsPdf = directPdfDownloadProviders.some(p => p.mst === provider.mst);
                const supportsAutoFill = directPdfDownloadProviders.some(p => p.mst === provider.mst && p.autoFillSupported);
                const pdfBadge = supportsPdf ? '<span class="provider-badge">PDF</span>' : '';
                const autoFillBadge = supportsAutoFill ? '<span class="provider-badge">Auto</span>' : '';
                
                tableHTML += `
                    <tr>
                        <td>${index + 1}</td>
                        <td>${provider.mst}</td>
                        <td>${provider.name} ${pdfBadge} ${autoFillBadge}</td>
                        <td><a href="${provider.url}" target="_blank" class="provider-link">${provider.url}</a></td>
                        <td>${supportsPdf ? '✅ Có' : '❌ Không'}</td>
                        <td>${supportsAutoFill ? '✅ Có' : '❌ Không'}</td>
                    </tr>
                `;
            });
            
            tableHTML += `
                    </tbody>
                </table>
            `;
            
            container.innerHTML = tableHTML;
        }

        // Hàm lưu XML vào file JSON - YÊU CẦU 1
        function saveXmlToJson(xmlContent, fileName) {
            try {
                // Tạo đối tượng JSON từ XML
                const jsonData = {
                    xmlContent: xmlContent,
                    fileName: fileName,
                    timestamp: new Date().toISOString(),
                    processedData: invoiceData
                };
                
                // Lưu vào localStorage thay vì tải về file
                localStorage.setItem('invoiceJsonData', JSON.stringify(jsonData));
                
                console.log('Đã lưu XML vào JSON trong localStorage:', fileName);
                return true;
            } catch (error) {
                console.error('Lỗi khi lưu XML vào JSON:', error);
                return false;
            }
        }

        // Hàm tải dữ liệu JSON từ localStorage
        function loadJsonFromStorage() {
            try {
                const jsonData = localStorage.getItem('invoiceJsonData');
                if (jsonData) {
                    return JSON.parse(jsonData);
                }
                return null;
            } catch (error) {
                console.error('Lỗi khi tải dữ liệu JSON từ localStorage:', error);
                return null;
            }
        }

        /**
         * THUẬT TOÁN MỚI: Trích xuất mã tra cứu portal từ hóa đơn điện tử Việt Nam
         * @param {string} xmlContent - Nội dung XML hóa đơn
         * @returns {Array} - Mảng các mã tra cứu tìm thấy
         */
        function extractPortalCodes(xmlContent) {
            try {
                const parser = new DOMParser();
                const xmlDoc = parser.parseFromString(xmlContent, "text/xml");
                
                const foundCodes = [];
                const processedCodes = new Set();

                // Lấy MSTTCGP để xác định ưu tiên
                const msttcgpElements = xmlDoc.getElementsByTagName("MSTTCGP");
                let msttcgp = "";
                if (msttcgpElements.length > 0) {
                    msttcgp = msttcgpElements[0].textContent.trim();
                }

                // 1. ƯU TIÊN CAO: Tìm theo MSTTCGP cụ thể
                if (msttcgp === "0101243150") {
                    // MISA: Ưu tiên thuộc tính ID của thẻ DLHDon
                    const dlhdonElements = xmlDoc.getElementsByTagName("DLHDon");
                    if (dlhdonElements.length > 0) {
                        const dlhdonId = dlhdonElements[0].getAttribute("Id");
                        if (dlhdonId && dlhdonId.trim() && !processedCodes.has(dlhdonId)) {
                            foundCodes.push({
                                code: dlhdonId.trim(),
                                field: "DLHDon Id",
                                type: "transaction_id",
                                source: "DLHDon",
                                priority: 1
                            });
                            processedCodes.add(dlhdonId);
                        }
                    }
                } else if (msttcgp === "0100684378" || msttcgp === "0105987432") {
                    // VNPT và EasyInvoice: Ưu tiên khóa Fkey
                    const ttinElements = xmlDoc.getElementsByTagName("TTin");
                    for (let element of ttinElements) {
                        const children = element.children;
                        let fieldName = '';
                        let dataValue = '';
                        
                        for (let child of children) {
                            if (child.tagName === 'TTruong') fieldName = child.textContent || '';
                            if (child.tagName === 'DLieu') dataValue = child.textContent || '';
                        }
                        
                        if (fieldName && fieldName.toLowerCase().includes('fkey') && 
                            dataValue && !processedCodes.has(dataValue)) {
                            foundCodes.push({
                                code: dataValue,
                                field: fieldName,
                                type: "fkey",
                                source: "TTKhac",
                                priority: 1
                            });
                            processedCodes.add(dataValue);
                        }
                    }
                } else {
                    // Các MSTTCGP còn lại: Ưu tiên thuộc tính ID của DLHDon và khóa Fkey
                    
                    // Ưu tiên 1: Thuộc tính ID của DLHDon
                    const dlhdonElements = xmlDoc.getElementsByTagName("DLHDon");
                    if (dlhdonElements.length > 0) {
                        const dlhdonId = dlhdonElements[0].getAttribute("Id");
                        if (dlhdonId && dlhdonId.trim() && !processedCodes.has(dlhdonId)) {
                            foundCodes.push({
                                code: dlhdonId.trim(),
                                field: "DLHDon Id",
                                type: "transaction_id",
                                source: "DLHDon",
                                priority: 1
                            });
                            processedCodes.add(dlhdonId);
                        }
                    }
                    
                    // Ưu tiên 2: Khóa Fkey
                    const ttinElements = xmlDoc.getElementsByTagName("TTin");
                    for (let element of ttinElements) {
                        const children = element.children;
                        let fieldName = '';
                        let dataValue = '';
                        
                        for (let child of children) {
                            if (child.tagName === 'TTruong') fieldName = child.textContent || '';
                            if (child.tagName === 'DLieu') dataValue = child.textContent || '';
                        }
                        
                        if (fieldName && fieldName.toLowerCase().includes('fkey') && 
                            dataValue && !processedCodes.has(dataValue)) {
                            foundCodes.push({
                                code: dataValue,
                                field: fieldName,
                                type: "fkey",
                                source: "TTKhac",
                                priority: 2
                            });
                            processedCodes.add(dataValue);
                        }
                    }
                }

                // 2. Tìm trong các trường TTKhac khác (ưu tiên thấp hơn)
                const ttinElements = xmlDoc.getElementsByTagName('TTin');
                for (let element of ttinElements) {
                    const children = element.children;
                    let fieldName = '';
                    let dataValue = '';
                    
                    for (let child of children) {
                        if (child.tagName === 'TTruong') fieldName = child.textContent || '';
                        if (child.tagName === 'DLieu') dataValue = child.textContent || '';
                    }
                    
                    if (dataValue && !processedCodes.has(dataValue) && isPortalCode(dataValue)) {
                        foundCodes.push({
                            code: dataValue,
                            field: fieldName,
                            type: getCodeType(fieldName),
                            source: 'TTKhac',
                            priority: 3
                        });
                        processedCodes.add(dataValue);
                    }
                }

                // 3. Tìm trong toàn bộ XML (dự phòng)
                const allText = xmlDoc.documentElement.textContent || '';
                const words = allText.split(/[\s<>]+/).filter(word => word.trim().length > 0);
                
                for (let word of words) {
                    const cleanWord = word.replace(/[^\w*\-_]/g, '');
                    if (isPortalCode(cleanWord) && !processedCodes.has(cleanWord)) {
                        foundCodes.push({
                            code: cleanWord,
                            field: 'auto_detected',
                            type: 'auto_detected',
                            source: 'text_scan',
                            priority: 4
                        });
                        processedCodes.add(cleanWord);
                    }
                }

                // Sắp xếp theo độ ưu tiên (priority thấp hơn = ưu tiên cao hơn)
                foundCodes.sort((a, b) => a.priority - b.priority);

                return foundCodes;

            } catch (error) {
                console.error('Lỗi phân tích XML:', error);
                return [];
            }
        }

        // Kiểm tra pattern mã tra cứu portal
        function isPortalCode(text) {
            if (!text || typeof text !== 'string') return false;
            if (text.length < 6 || text.length > 25) return false;
            
            // Loại bỏ các mã hex dài (MCCQT)
            if (/^[0-9A-F]{32,}$/i.test(text)) return false;
            
            // Pattern cho mã tra cứu portal
            const patterns = [
                /^[A-Za-z0-9*\-_]{6,25}$/,           // Chữ + số + ký tự đặc biệt
                /^(?=.*[A-Z])(?=.*[a-z])(?=.*[0-9])/, // Có cả chữ hoa, thường và số
                /^[A-Z0-9]{6,20}[*]?$/,              // Toàn chữ hoa + số, có thể có *
                /^[a-z0-9]{6,20}$/,                  // Toàn chữ thường + số
            ];
            
            return patterns.some(pattern => pattern.test(text));
        }

        // Hàm hỗ trợ phân loại mã
        function getCodeType(fieldName) {
            if (!fieldName) return 'unknown';
            
            const field = fieldName.toLowerCase();
            if (field.includes('fkey')) return 'fkey';
            if (field.includes('portal')) return 'portal';
            if (field.includes('secret') || field.includes('bí mật')) return 'secret';
            if (field.includes('tra cứu') || field.includes('tracuu')) return 'tra_cuu';
            
            return 'other';
        }

        // Thuật toán tìm kiếm Transaction ID được cải tiến
        function findTransactionID(xmlDoc) {
            console.log("🔍 Bắt đầu tìm kiếm Transaction ID...");
            
            // ƯU TIÊN CAO NHẤT: Tìm trong thuộc tính Id của thẻ DLHDon
            const dlhdonElements = xmlDoc.getElementsByTagName('DLHDon');
            if (dlhdonElements.length > 0) {
                const dlhdonId = dlhdonElements[0].getAttribute('Id');
                if (dlhdonId && dlhdonId.trim()) {
                    console.log(`✅ Tìm thấy Transaction ID trong thuộc tính Id của DLHDon: ${dlhdonId}`);
                    return dlhdonId.trim();
                }
            }
            
            // Pattern cho Transaction ID: chuỗi chữ và số, có thể có dấu gạch ngang
            const transactionIdPattern = /^[A-Za-z0-9\-_]+$/;
            
            // Tìm kiếm trong toàn bộ cấu trúc XML
            const allElements = xmlDoc.getElementsByTagName('*');
            const candidates = [];
            
            for (let i = 0; i < allElements.length; i++) {
                const element = allElements[i];
                const textContent = element.textContent.trim();
                
                // Kiểm tra nếu nội dung phù hợp với pattern transaction ID
                if (textContent && transactionIdPattern.test(textContent)) {
                    // Kiểm tra độ dài hợp lý (thường từ 10-50 ký tự)
                    if (textContent.length >= 10 && textContent.length <= 50) {
                        console.log(`✅ Tìm thấy ứng viên Transaction ID: ${textContent} (tag: ${element.tagName})`);
                        candidates.push({
                            value: textContent,
                            tagName: element.tagName,
                            parentTag: element.parentNode ? element.parentNode.tagName : 'N/A'
                        });
                    }
                }
            }
            
            // Ưu tiên các tag có tên liên quan đến transaction ID
            const priorityTags = ['TransactionID', 'InvoiceGUID', 'GUID', 'UUID', 'MaGiaoDich', 'Reference', 'Id', 'ID'];
            
            for (const candidate of candidates) {
                if (priorityTags.includes(candidate.tagName)) {
                    console.log(`🎯 Tìm thấy Transaction ID trong tag ưu tiên '${candidate.tagName}': ${candidate.value}`);
                    return candidate.value;
                }
            }
            
            // Tìm trong TTKhac với thuật toán cải tiến
            const ttKhac = xmlDoc.getElementsByTagName('TTKhac');
            if (ttKhac.length > 0) {
                console.log("🔍 Đang tìm trong TTKhac...");
                const ttinList = ttKhac[0].getElementsByTagName('TTin');
                for (let i = 0; i < ttinList.length; i++) {
                    const ttruong = ttinList[i].getElementsByTagName('TTruong')[0];
                    const dlieu = ttinList[i].getElementsByTagName('DLieu')[0];
                    
                    if (ttruong && dlieu) {
                        const fieldName = ttruong.textContent.toLowerCase();
                        const fieldValue = dlieu.textContent.trim();
                        
                        // Kiểm tra các từ khóa liên quan đến transaction ID
                        if (fieldValue && transactionIdPattern.test(fieldValue) && 
                            (fieldName.includes('transaction') ||
                             fieldName.includes('guid') ||
                             fieldName.includes('invoiceid') ||
                             fieldName.includes('invoiceguid') ||
                             fieldName.includes('uuid') ||
                             fieldName.includes('mã giao dịch') ||
                             fieldName.includes('mã hóa đơn') ||
                             fieldName.includes('số biên nhận') ||
                             fieldName.includes('reference') ||
                             fieldName.includes('id'))) {
                            console.log(`✅ Tìm thấy Transaction ID trong TTKhac: ${fieldValue}`);
                            return fieldValue;
                        }
                    }
                }
            }
            
            // Nếu vẫn không tìm thấy, trả về candidate đầu tiên (nếu có)
            if (candidates.length > 0) {
                console.log(`🔄 Sử dụng candidate đầu tiên: ${candidates[0].value}`);
                return candidates[0].value;
            }
            
            // Fallback: sử dụng số hóa đơn nếu không tìm thấy transaction ID
            if (invoiceData.invoiceNo) {
                console.log(`🔄 Sử dụng số hóa đơn làm Transaction ID: ${invoiceData.invoiceNo}`);
                return invoiceData.invoiceNo;
            }
            
            console.log("❌ Không tìm thấy Transaction ID");
            return '';
        }

        // Hàm xử lý XML chung để trích xuất thông tin
        function processXmlData(xmlContent) {
            try {
                const parser = new DOMParser();
                const xmlDoc = parser.parseFromString(xmlContent, 'text/xml');
                
                // Kiểm tra lỗi XML
                const parseError = xmlDoc.getElementsByTagName('parsererror');
                if (parseError.length > 0) {
                    throw new Error('File XML không hợp lệ');
                }
                
                // Trích xuất thông tin cơ bản
                const sellerName = xmlDoc.querySelector('NBan > Ten')?.textContent || '';
                const sellerTaxCode = xmlDoc.querySelector('NBan > MST')?.textContent || '';
                const serviceProviderTaxCode = xmlDoc.querySelector('MSTTCGP')?.textContent || '';
                
                // Sử dụng thuật toán mới để tìm mã tra cứu
                const portalCodes = extractPortalCodes(xmlContent);
                const verificationCode = portalCodes.length > 0 ? portalCodes[0].code : '';
                
                // Sử dụng thuật toán cải tiến để tìm transaction ID
                const transactionID = findTransactionID(xmlDoc);
                
                const invoiceNumber = xmlDoc.querySelector('SHDon')?.textContent || '';
                
                // Cập nhật dữ liệu toàn cục
                invoiceData = {
                    sellerName,
                    sellerTaxCode,
                    serviceProviderTaxCode,
                    verificationCode,
                    transactionID,
                    invoiceNumber,
                    xmlContent: xmlContent,
                    portalCodes: portalCodes // Lưu tất cả các mã tìm thấy
                };
                
                console.log("📊 Dữ liệu hóa đơn đã được xử lý:", invoiceData);
                console.log("🔍 Các mã tra cứu tìm thấy:", portalCodes);
                
                // Tìm nhà cung cấp phù hợp - YÊU CẦU 2
                matchedProviders = findMatchingProviders(serviceProviderTaxCode);
                
                // Lưu vào JSON - YÊU CẦU 1
                saveXmlToJson(xmlContent, 'invoice_data');
                
                return invoiceData;
            } catch (error) {
                console.error('Lỗi khi xử lý XML:', error);
                return null;
            }
        }

        // ==============================================
        // PHẦN XỬ LÝ CHO TAB 1: VĨNH HY PDF GENERATOR
        // ==============================================

        // DOM Elements cho tab 1
        const xmlFileLink = document.getElementById('xmlFileLink');
        const fileNameLink = document.getElementById('fileNameLink');
        const generateLinkBtn = document.getElementById('generateLinkBtn');
        const loadingLink = document.getElementById('loadingLink');
        const linkArea = document.getElementById('linkArea');
        const pdfLink = document.getElementById('pdfLink');
        const copyButton = document.getElementById('copyButton');
        const downloadLink = document.getElementById('downloadLink');
        const invoiceInfo = document.getElementById('invoiceInfo');
        const debugInfo = document.getElementById('debugInfo');
        const statusMessage = document.getElementById('statusMessage');

        // Event Listeners cho tab 1
        xmlFileLink.addEventListener('change', function(e) {
            const file = e.target.files[0];
            if (!file) return;
            
            const fileName = file.name;
            fileNameLink.textContent = `File đã chọn: ${fileName}`;
            statusMessage.style.display = 'none';
            
            // Đọc và xử lý XML - YÊU CẦU 1
            const reader = new FileReader();
            reader.onload = function(e) {
                const xmlContent = e.target.result;
                processXmlData(xmlContent); // Xử lý và lưu vào JSON
            };
            reader.readAsText(file);
        });

        generateLinkBtn.addEventListener('click', function() {
            const file = xmlFileLink.files[0];
            
            if (!file) {
                showStatus('Vui lòng chọn file XML trước khi tạo link!', 'error');
                return;
            }
            
            loadingLink.style.display = 'block';
            linkArea.style.display = 'none';
            statusMessage.style.display = 'none';
            
            const reader = new FileReader();
            
            reader.onload = function(e) {
                setTimeout(() => {
                    const xmlContent = e.target.result;
                    const invoiceData = extractInvoiceDataForLink(xmlContent);
                    
                    if (invoiceData.invoiceId) {
                        showPDFLinkForTab1(invoiceData);
                        showStatus('Đã tạo link PDF thành công!', 'success');
                    } else {
                        showStatus('Không tìm thấy thông tin hóa đơn trong XML', 'error');
                    }
                    
                    loadingLink.style.display = 'none';
                }, 500);
            };
            
            reader.onerror = function() {
                loadingLink.style.display = 'none';
                showStatus('Lỗi đọc file. Vui lòng thử lại.', 'error');
            };
            
            reader.readAsText(file);
        });

        // Hàm trích xuất dữ liệu hóa đơn cho tab 1
        function extractInvoiceDataForLink(xmlContent) {
            try {
                const parser = new DOMParser();
                const xmlDoc = parser.parseFromString(xmlContent, "text/xml");
                
                // Kiểm tra lỗi XML
                const parseError = xmlDoc.getElementsByTagName("parsererror")[0];
                if (parseError) {
                    throw new Error('File XML không hợp lệ');
                }
                
                // Lấy ID đầy đủ
                const dlhdon = xmlDoc.querySelector('DLHDon');
                const invoiceId = dlhdon ? dlhdon.getAttribute('Id') : null;
                
                // Lấy thông tin bổ sung
                const sellerName = getXmlText(xmlDoc, 'NBan Ten');
                const buyerName = getXmlText(xmlDoc, 'NMua Ten');
                const invoiceNumber = getXmlText(xmlDoc, 'SHDon');
                const issueDate = getXmlText(xmlDoc, 'NLap');
                const totalAmount = getXmlText(xmlDoc, 'TgTTTBSo');
                const totalAmountText = getXmlText(xmlDoc, 'TgTTTBChu');
                const sellerMST = getXmlText(xmlDoc, 'NBan MST');
                const buyerMST = getXmlText(xmlDoc, 'NMua MST');
                const pattern = getXmlText(xmlDoc, 'KHHDon');
                const symbol = getXmlText(xmlDoc, 'KHMSHDon');
                const serviceProviderTaxCode = getXmlText(xmlDoc, 'MSTTCGP');
                
                return {
                    invoiceId: invoiceId,
                    sellerName: sellerName,
                    buyerName: buyerName,
                    invoiceNumber: invoiceNumber,
                    issueDate: issueDate,
                    totalAmount: totalAmount,
                    totalAmountText: totalAmountText,
                    sellerMST: sellerMST,
                    buyerMST: buyerMST,
                    pattern: pattern,
                    symbol: symbol,
                    rawId: invoiceId,
                    serviceProviderTaxCode: serviceProviderTaxCode
                };
            } catch (error) {
                console.error('Lỗi phân tích XML:', error);
                return { invoiceId: null };
            }
        }

        // Hàm lấy text từ XML
        function getXmlText(xmlDoc, tagName) {
            const element = xmlDoc.querySelector(tagName);
            return element ? element.textContent : '';
        }

        // Hàm hiển thị link PDF cho tab 1
        function showPDFLinkForTab1(invoiceData) {
            // Định dạng ID theo chuẩn chính xác - LINH HOẠT CHO MỌI MST VÀ NĂM
            const formattedId = formatInvoiceIdForLink(invoiceData.rawId, invoiceData.sellerMST);
            const pdfUrl = `${VIENH_HY_MODULE.pdfGeneratorUrl}?r=${formattedId}&type=pdf`;
            
            // Hiển thị link
            pdfLink.textContent = pdfUrl;
            downloadLink.href = pdfUrl;
            
            // Hiển thị thông tin hóa đơn
            displayInvoiceInfoForTab1(invoiceData, formattedId);
            
            // Hiển thị thông tin debug
            displayDebugInfoForTab1(invoiceData, formattedId);
            
            // Hiển thị khu vực link
            linkArea.style.display = 'block';
            linkArea.scrollIntoView({ behavior: 'smooth' });
            
            // Cập nhật link tra cứu ở footer
            updateFooterLookupLink(pdfUrl, 'Vĩnh Hy PDF Generator');
        }

        // Hàm định dạng ID hóa đơn cho tab 1
        function formatInvoiceIdForLink(rawId, sellerMST) {
            if (!rawId) return '';
            
            console.log('Raw ID:', rawId);
            console.log('ID Length:', rawId.length);
            console.log('Seller MST:', sellerMST);
            
            // Phân tích ID theo cấu trúc thực tế
            // MST (10 ký tự) + Năm (4 ký tự) + Số hóa đơn (12 ký tự)
            let mst, year, invoiceNumber;
            
            if (rawId.length === 24) {
                // Định dạng chuẩn: 031623981320200300000018
                mst = rawId.substring(0, 10); // 0316239813
                year = rawId.substring(10, 14); // 2020
                invoiceNumber = rawId.substring(14); // 0300000018
            } else if (rawId.length === 26) {
                // Định dạng khác: 03162398132020030000001800
                mst = rawId.substring(0, 10); // 0316239813
                year = rawId.substring(10, 14); // 2020
                invoiceNumber = rawId.substring(14, 24); // 0300000018
            } else {
                // Định dạng không xác định, sử dụng sellerMST nếu có
                mst = sellerMST || '0000000000';
                year = '2020';
                invoiceNumber = rawId;
            }
            
            // Đảm bảo MST có 10 ký tự
            if (mst.length < 10) {
                mst = mst.padStart(10, '0');
            }
            
            // Đảm bảo invoiceNumber có 10 ký tự
            if (invoiceNumber.length < 10) {
                invoiceNumber = invoiceNumber.padStart(10, '0');
            }
            
            const formatted = `ct_${mst}${year}_${invoiceNumber}`;
            console.log('Formatted ID:', formatted);
            
            return formatted;
        }

        // Hàm hiển thị thông tin hóa đơn cho tab 1
        function displayInvoiceInfoForTab1(invoiceData, formattedId) {
            const infoHtml = `
                <div class="info-item">
                    <span class="info-label">Người bán:</span>
                    <span class="info-value">${invoiceData.sellerName}</span>
                </div>
                <div class="info-item">
                    <span class="info-label">MST người bán:</span>
                    <span class="info-value">${invoiceData.sellerMST || 'N/A'}</span>
                </div>
                <div class="info-item">
                    <span class="info-label">Người mua:</span>
                    <span class="info-value">${invoiceData.buyerName}</span>
                </div>
                <div class="info-item">
                    <span class="info-label">MST người mua:</span>
                    <span class="info-value">${invoiceData.buyerMST || 'N/A'}</span>
                </div>
                <div class="info-item">
                    <span class="info-label">Số hóa đơn:</span>
                    <span class="info-value">${invoiceData.invoiceNumber}</span>
                </div>
                <div class="info-item">
                    <span class="info-label">Ký hiệu:</span>
                    <span class="info-value">${invoiceData.pattern || 'N/A'}/${invoiceData.symbol || 'N/A'}</span>
                </div>
                <div class="info-item">
                    <span class="info-label">Ngày lập:</span>
                    <span class="info-value">${formatDateForTab1(invoiceData.issueDate)}</span>
                </div>
                <div class="info-item">
                    <span class="info-label">Tổng tiền:</span>
                    <span class="info-value">${formatCurrencyForTab1(invoiceData.totalAmount)}</span>
                </div>
                <div class="info-item">
                    <span class="info-label">MSTTCGP:</span>
                    <span class="info-value">${invoiceData.serviceProviderTaxCode || 'N/A'}</span>
                </div>
                <div class="info-item">
                    <span class="info-label">ID định dạng:</span>
                    <span class="info-value">${formattedId}</span>
                </div>
            `;
            invoiceInfo.innerHTML = infoHtml;
        }

        // Hàm hiển thị thông tin debug cho tab 1
        function displayDebugInfoForTab1(invoiceData, formattedId) {
            const debugHtml = `
                <strong>Thông tin Debug:</strong><br>
                <strong>Raw ID:</strong> ${invoiceData.rawId}<br>
                <strong>Độ dài ID:</strong> ${invoiceData.rawId ? invoiceData.rawId.length : 0}<br>
                <strong>Formatted ID:</strong> ${formattedId}<br>
                <strong>MST:</strong> ${invoiceData.rawId ? invoiceData.rawId.substring(0, 10) : ''}<br>
                <strong>Năm:</strong> ${invoiceData.rawId ? invoiceData.rawId.substring(10, 14) : ''}<br>
                <strong>Số HĐ:</strong> ${invoiceData.rawId ? invoiceData.rawId.substring(14) : ''}<br>
                <strong>MSTTCGP:</strong> ${invoiceData.serviceProviderTaxCode || 'N/A'}<br>
                <strong>URL đầy đủ:</strong> ${VIENH_HY_MODULE.pdfGeneratorUrl}?r=${formattedId}&type=pdf
            `;
            debugInfo.innerHTML = debugHtml;
        }

        // Hàm hiển thị trạng thái cho tab 1
        function showStatus(message, type) {
            statusMessage.textContent = message;
            statusMessage.className = 'status-message';
            statusMessage.classList.add(type === 'success' ? 'status-success' : 'status-error');
            statusMessage.style.display = 'block';
            
            setTimeout(() => {
                statusMessage.style.display = 'none';
            }, 5000);
        }

        // Hàm copy link cho tab 1
        function copyLink() {
            const linkText = pdfLink.textContent;
            
            if (navigator.clipboard && window.isSecureContext) {
                navigator.clipboard.writeText(linkText).then(function() {
                    showCopySuccessForTab1();
                }).catch(function() {
                    fallbackCopyTextForTab1(linkText);
                });
            } else {
                fallbackCopyTextForTab1(linkText);
            }
        }

        // Hàm fallback copy cho tab 1
        function fallbackCopyTextForTab1(text) {
            const textArea = document.createElement("textarea");
            textArea.value = text;
            textArea.style.position = "fixed";
            textArea.style.left = "-999999px";
            textArea.style.top = "-999999px";
            document.body.appendChild(textArea);
            textArea.focus();
            textArea.select();
            
            try {
                const successful = document.execCommand('copy');
                if (successful) {
                    showCopySuccessForTab1();
                } else {
                    showStatus('Không thể copy link. Vui lòng copy thủ công bằng Ctrl+C', 'error');
                }
            } catch (err) {
                showStatus('Lỗi copy link. Vui lòng copy thủ công bằng Ctrl+C', 'error');
            }
            
            document.body.removeChild(textArea);
        }

        // Hàm hiển thị thành công copy cho tab 1
        function showCopySuccessForTab1() {
            const originalText = copyButton.innerHTML;
            copyButton.innerHTML = '✅ Đã Copy!';
            copyButton.classList.add('copied');
            copyButton.classList.add('pulse');
            
            showStatus('✅ Đã copy link thành công! Hãy dán vào trình duyệt để tải PDF.', 'success');
            
            setTimeout(() => {
                copyButton.innerHTML = originalText;
                copyButton.classList.remove('copied');
                copyButton.classList.remove('pulse');
            }, 2000);
        }

        // Hàm chọn text link cho tab 1
        function selectLinkText() {
            const selection = window.getSelection();
            const range = document.createRange();
            range.selectNodeContents(pdfLink);
            selection.removeAllRanges();
            selection.addRange(range);
        }

        // Hàm toggle debug info cho tab 1
        function toggleDebug() {
            debugInfo.style.display = debugInfo.style.display === 'none' ? 'block' : 'none';
        }

        // Hàm utility cho tab 1
        function formatDateForTab1(dateString) {
            if (!dateString) return 'N/A';
            try {
                const date = new Date(dateString);
                return date.toLocaleDateString('vi-VN');
            } catch {
                return dateString;
            }
        }

        function formatCurrencyForTab1(amount) {
            if (!amount) return '0';
            return parseInt(amount).toLocaleString('vi-VN');
        }

        // Hỗ trợ kéo thả cho tab 1
        const uploadAreaLink = document.querySelector('#link-generator .upload-section');
        
        uploadAreaLink.addEventListener('dragover', (e) => {
            e.preventDefault();
            uploadAreaLink.style.background = '#e3f2fd';
        });

        uploadAreaLink.addEventListener('dragleave', (e) => {
            e.preventDefault();
            uploadAreaLink.style.background = '#f8f9fa';
        });

        uploadAreaLink.addEventListener('drop', (e) => {
            e.preventDefault();
            uploadAreaLink.style.background = '#f8f9fa';
            const file = e.dataTransfer.files[0];
            if (file && file.name.endsWith('.xml')) {
                xmlFileLink.files = e.dataTransfer.files;
                const fileName = file.name;
                fileNameLink.textContent = `File đã chọn: ${fileName}`;
                statusMessage.style.display = 'none';
                
                // Đọc và xử lý XML - YÊU CẦU 1
                const reader = new FileReader();
                reader.onload = function(e) {
                    const xmlContent = e.target.result;
                    processXmlData(xmlContent); // Xử lý và lưu vào JSON
                };
                reader.readAsText(file);
            } else {
                showStatus('Vui lòng chọn file XML', 'error');
            }
        });

        // ==============================================
        // PHẦN XỬ LÝ CHO TAB 2: MODULE MISA
        // ==============================================

        // DOM Elements cho tab 2
        const fileInputMisa = document.getElementById('file-input-misa');
        const fileUploadAreaMisa = document.getElementById('file-upload-area');
        const fileInfoMisa = document.getElementById('file-info-misa');
        const fileNameMisa = document.getElementById('file-name-misa');
        const fileSizeMisa = document.getElementById('file-size-misa');
        const processingMisa = document.getElementById('processing-misa');
        const processBtnMisa = document.getElementById('process-btn-misa');
        const invoiceDetailsMisa = document.getElementById('invoice-details-misa');
        const linkSectionMisa = document.getElementById('link-section-misa');
        const pdfLinkMisa = document.getElementById('pdf-link-misa');
        const openLinkMisa = document.getElementById('open-link-misa');

        // Biến lưu trữ file hiện tại cho tab MISA
        let currentFileMisa = null;

        // Event Listeners cho tab 2
        fileUploadAreaMisa.addEventListener('click', function() {
            fileInputMisa.click();
        });

        fileInputMisa.addEventListener('change', function(e) {
            handleFileSelectMisa(e.target.files[0]);
        });

        processBtnMisa.addEventListener('click', function() {
            processUploadedFileMisa();
        });

        // Hàm xử lý chọn file cho tab MISA
        function handleFileSelectMisa(file) {
            if (!file) return;
            
            // Validate file type
            if (!file.name.toLowerCase().endsWith('.xml') && !file.name.toLowerCase().endsWith('.txt')) {
                showMessageMisa('Chỉ chấp nhận file XML (.xml) hoặc text (.txt)', 'error');
                return;
            }
            
            // Validate file size (max 5MB)
            if (file.size > 5 * 1024 * 1024) {
                showMessageMisa('File quá lớn. Tối đa 5MB', 'error');
                return;
            }
            
            currentFileMisa = file;
            
            // Display file info
            fileNameMisa.textContent = file.name;
            fileSizeMisa.textContent = formatFileSizeMisa(file.size);
            fileInfoMisa.style.display = 'block';
            processBtnMisa.disabled = false;
            
            showMessageMisa('✅ Đã chọn file: ' + file.name, 'success');
            
            // Đọc và xử lý XML - YÊU CẦU 1
            const reader = new FileReader();
            reader.onload = function(e) {
                const xmlContent = e.target.result;
                processXmlData(xmlContent); // Xử lý và lưu vào JSON
            };
            reader.readAsText(file);
        }

        // Hàm xử lý file đã tải lên cho tab MISA
        async function processUploadedFileMisa() {
            if (!currentFileMisa) {
                showMessageMisa('Vui lòng chọn file XML trước', 'error');
                return;
            }
            
            // Show processing
            processingMisa.style.display = 'block';
            processBtnMisa.disabled = true;
            
            try {
                const fileContent = await readFileContentMisa(currentFileMisa);
                const invoiceData = parseXMLDataMisa(fileContent);
                displayInvoiceDetailsMisa(invoiceData);
                generateAndDisplayLinkMisa(invoiceData.sellerTaxCode, invoiceData.transactionId);
            } catch (error) {
                showMessageMisa('Lỗi xử lý file: ' + error.message, 'error');
            } finally {
                processingMisa.style.display = 'none';
                processBtnMisa.disabled = false;
            }
        }

        // Hàm đọc nội dung file cho tab MISA
        function readFileContentMisa(file) {
            return new Promise((resolve, reject) => {
                const reader = new FileReader();
                reader.onload = e => resolve(e.target.result);
                reader.onerror = () => reject(new Error('Không thể đọc file'));
                reader.readAsText(file);
            });
        }

        // Hàm phân tích XML cho tab MISA
        function parseXMLDataMisa(xmlString) {
            try {
                const parser = new DOMParser();
                const xmlDoc = parser.parseFromString(xmlString, 'text/xml');
                
                // Check for XML errors
                const parseError = xmlDoc.getElementsByTagName('parsererror')[0];
                if (parseError) {
                    throw new Error('Cấu trúc XML không hợp lệ');
                }
                
                // Extract required data
                const sellerTaxCode = xmlDoc.querySelector('NBan > MST')?.textContent;
                const transactionId = xmlDoc.querySelector('DLHDon')?.getAttribute('Id');
                const invoiceNumber = xmlDoc.querySelector('SHDon')?.textContent;
                const invoiceSymbol = xmlDoc.querySelector('KHHDon')?.textContent;
                const issueDate = xmlDoc.querySelector('NLap')?.textContent;
                const sellerName = xmlDoc.querySelector('NBan > Ten')?.textContent;
                const buyerName = xmlDoc.querySelector('NMua > Ten')?.textContent;
                const serviceProviderTaxCode = xmlDoc.querySelector('MSTTCGP')?.textContent;
                
                if (!sellerTaxCode) {
                    throw new Error('Không tìm thấy MST người bán trong XML');
                }
                
                if (!transactionId) {
                    throw new Error('Không tìm thấy Transaction ID trong XML');
                }
                
                return {
                    sellerTaxCode,
                    transactionId,
                    invoiceNumber,
                    invoiceSymbol,
                    issueDate,
                    sellerName,
                    buyerName,
                    serviceProviderTaxCode
                };
            } catch (error) {
                throw new Error('Lỗi phân tích XML: ' + error.message);
            }
        }

        // Hàm hiển thị chi tiết hóa đơn cho tab MISA
        function displayInvoiceDetailsMisa(invoiceData) {
            document.getElementById('detail-sellerTaxCode').textContent = invoiceData.sellerTaxCode || '-';
            document.getElementById('detail-transactionId').textContent = invoiceData.transactionId || '-';
            document.getElementById('detail-invoiceNumber').textContent = invoiceData.invoiceNumber || '-';
            document.getElementById('detail-invoiceSymbol').textContent = invoiceData.invoiceSymbol || '-';
            document.getElementById('detail-issueDate').textContent = invoiceData.issueDate || '-';
            
            invoiceDetailsMisa.style.display = 'block';
        }

        // Hàm tạo và hiển thị link PDF cho tab MISA
        function generateAndDisplayLinkMisa(sellerTaxCode, transactionId) {
            // Generate PDF link với module code
            const pdfLink = `${MISA_MODULE.pdfGeneratorUrl}?Type=pdf&MST=${sellerTaxCode}&Code=${transactionId}&Module=${MISA_MODULE.mst}`;
            
            // Display link section
            pdfLinkMisa.value = pdfLink;
            openLinkMisa.href = pdfLink;
            linkSectionMisa.style.display = 'block';
            
            showMessageMisa('✅ Đã tạo link PDF thành công! Module: ' + MISA_MODULE.mst, 'success');
            
            // Cập nhật link tra cứu ở footer
            updateFooterLookupLink(pdfLink, 'MISA PDF Generator');
        }

        // Hàm copy link cho tab MISA
        function copyLinkMisa() {
            const linkInput = pdfLinkMisa;
            linkInput.select();
            linkInput.setSelectionRange(0, 99999);
            
            try {
                navigator.clipboard.writeText(linkInput.value).then(() => {
                    showMessageMisa('📋 Đã copy link vào clipboard!', 'success');
                }).catch(err => {
                    fallbackCopyTextMisa(linkInput.value);
                });
            } catch (err) {
                fallbackCopyTextMisa(linkInput.value);
            }
        }

        // Hàm fallback copy cho tab MISA
        function fallbackCopyTextMisa(text) {
            const textArea = document.createElement('textarea');
            textArea.value = text;
            document.body.appendChild(textArea);
            textArea.select();
            document.execCommand('copy');
            document.body.removeChild(textArea);
            showMessageMisa('📋 Đã copy link vào clipboard!', 'success');
        }

        // Hàm hiển thị thông báo cho tab MISA
        function showMessageMisa(text, type) {
            const message = document.createElement('div');
            message.className = `message ${type}`;
            message.textContent = text;
            message.style.cssText = `
                position: fixed;
                top: 20px;
                right: 20px;
                padding: 16px 20px;
                border-radius: 10px;
                color: white;
                font-weight: 700;
                z-index: 1000;
                animation: slideIn 0.3s ease;
                box-shadow: 0 8px 25px rgba(0, 0, 0, 0.15);
                font-size: 14px;
            `;
            
            if (type === 'success') {
                message.style.background = 'linear-gradient(135deg, #28a745 0%, #20c997 100%)';
            } else {
                message.style.background = 'linear-gradient(135deg, #dc3545 0%, #fd7e14 100%)';
            }
            
            document.body.appendChild(message);
            
            setTimeout(() => {
                message.style.display = 'none';
                document.body.removeChild(message);
            }, 4000);
        }

        // Hàm định dạng kích thước file cho tab MISA
        function formatFileSizeMisa(bytes) {
            if (bytes === 0) return '0 Bytes';
            const k = 1024;
            const sizes = ['Bytes', 'KB', 'MB'];
            const i = Math.floor(Math.log(bytes) / Math.log(k));
            return parseFloat((bytes / Math.pow(k, i)).toFixed(2)) + ' ' + sizes[i];
        }

        // Hỗ trợ kéo thả cho tab MISA
        fileUploadAreaMisa.addEventListener('dragover', (e) => {
            e.preventDefault();
            fileUploadAreaMisa.classList.add('dragover');
        });

        fileUploadAreaMisa.addEventListener('dragleave', (e) => {
            e.preventDefault();
            fileUploadAreaMisa.classList.remove('dragover');
        });

        fileUploadAreaMisa.addEventListener('drop', (e) => {
            e.preventDefault();
            fileUploadAreaMisa.classList.remove('dragover');
            
            if (e.dataTransfer.files.length > 0) {
                handleFileSelectMisa(e.dataTransfer.files[0]);
            }
        });

        // ==============================================
        // PHẦN XỬ LÝ CHO TAB 3: MODULE BKAV
        // ==============================================

        // Xác thực XML khi nhập liệu cho tab BKAV
        function validateXMLBkav() {
            const xmlContent = document.getElementById('xmlInputBkav').value.trim();
            const generateBtn = document.getElementById('generateBtnBkav');
            const validationMessage = document.getElementById('validationMessageBkav');
            
            if (!xmlContent) {
                validationMessage.style.display = 'none';
                generateBtn.disabled = true;
                return;
            }
            
            const authResult = BKAV_MODULE.authenticate(xmlContent);
            
            if (authResult.success) {
                validationMessage.style.display = 'block';
                validationMessage.className = 'validation-message validation-success';
                validationMessage.innerHTML = authResult.message;
                generateBtn.disabled = false;
                
                // Xử lý và lưu XML vào JSON - YÊU CẦU 1
                processXmlData(xmlContent);
            } else {
                validationMessage.style.display = 'block';
                validationMessage.className = 'validation-message validation-error';
                validationMessage.innerHTML = authResult.error;
                generateBtn.disabled = true;
            }
        }

        // Hàm chính tạo link PDF cho tab BKAV
        function generatePDFLinkBkav() {
            const xmlContent = document.getElementById('xmlInputBkav').value.trim();
            
            if (!xmlContent) {
                alert('Vui lòng nhập nội dung XML!');
                return;
            }
            
            // Hiển thị trạng thái xử lý
            const resultContainer = document.getElementById('resultContainerBkav');
            resultContainer.style.display = 'block';
            document.getElementById('downloadLinkBkav').textContent = '🔄 Đang xử lý...';
            document.getElementById('downloadLinkBkav').href = '#';
            
            // Xử lý bất đồng bộ để hiển thị trạng thái
            setTimeout(() => {
                const result = BKAV_MODULE.generateFromXML(xmlContent);
                
                if (result.success) {
                    const link = result.data.downloadLink;
                    document.getElementById('downloadLinkBkav').textContent = link;
                    document.getElementById('downloadLinkBkav').href = link;
                    
                    // Hiển thị thông tin chi tiết
                    displayLinkInfoBkav(result.data);
                    
                    // Cập nhật link tra cứu ở footer
                    updateFooterLookupLink(link, 'BKAV PDF Generator');
                } else {
                    document.getElementById('downloadLinkBkav').textContent = '❌ Lỗi: ' + result.error;
                    document.getElementById('downloadLinkBkav').href = '#';
                    document.getElementById('linkInfoBkav').innerHTML = 
                        '<div style="color: #e74c3c; text-align: center;">' + result.error + '</div>';
                }
            }, 500);
        }
        
        // Hiển thị thông tin link cho tab BKAV
        function displayLinkInfoBkav(data) {
            const linkInfo = document.getElementById('linkInfoBkav');
            linkInfo.innerHTML = `
                <div class="bkav-info-item">
                    <span class="bkav-info-label">MSTTCGP:</span>
                    <span class="bkav-info-value">${data.msttcgp}</span>
                </div>
                <div class="bkav-info-item">
                    <span class="bkav-info-label">Ký hiệu hóa đơn:</span>
                    <span class="bkav-info-value">${data.kyyHieu}</span>
                </div>
                <div class="bkav-info-item">
                    <span class="bkav-info-label">Số hóa đơn:</span>
                    <span class="bkav-info-value">${data.soHoaDon}</span>
                </div>
                <div class="bkav-info-item">
                    <span class="bkav-info-label">Mã tra cứu (MTC):</span>
                    <span class="bkav-info-value">${data.maTraCuu}</span>
                </div>
                <div class="bkav-info-item">
                    <span class="bkav-info-label">Đường dẫn file:</span>
                    <span class="bkav-info-value">${data.filePath}</span>
                </div>
                <div class="bkav-info-item">
                    <span class="bkav-info-label">Trạng thái:</span>
                    <span class="bkav-info-value" style="color: #27ae60;">✅ Đã xác thực</span>
                </div>
            `;
        }
        
        // Xử lý upload file cho tab BKAV
        function handleFileUploadBkav(files) {
            if (files.length === 0) return;
            
            const file = files[0];
            if (file.type !== 'text/xml' && !file.name.endsWith('.xml')) {
                alert('Vui lòng chọn file XML!');
                return;
            }
            
            const reader = new FileReader();
            reader.onload = function(e) {
                document.getElementById('xmlInputBkav').value = e.target.result;
                validateXMLBkav();
            };
            reader.readAsText(file);
        }
        
        // Sao chép link vào clipboard cho tab BKAV
        function copyToClipboardBkav() {
            const link = document.getElementById('downloadLinkBkav').href;
            if (link && link !== '#') {
                navigator.clipboard.writeText(link).then(() => {
                    alert('✅ Đã sao chép link vào clipboard!');
                }).catch(() => {
                    // Fallback cho các trình duyệt cũ
                    const tempInput = document.createElement('input');
                    tempInput.value = link;
                    document.body.appendChild(tempInput);
                    tempInput.select();
                    document.execCommand('copy');
                    document.body.removeChild(tempInput);
                    alert('✅ Đã sao chép link vào clipboard!');
                });
            }
        }
        
        // Kiểm tra link cho tab BKAV
        function testDownloadLinkBkav() {
            const link = document.getElementById('downloadLinkBkav').href;
            if (link && link !== '#') {
                window.open(link, '_blank');
            } else {
                alert('Link không hợp lệ!');
            }
        }
        
        // Load XML mẫu cho tab BKAV
        function loadSampleXMLBkav() {
            const sampleXML = `<HDon>
    <DLHDon Id="72dca111-b41c-459f-8330-593753e19861">
        <TTChung>
            <PBan>2.1.0</PBan>
            <THDon>Hóa đơn giá trị gia tăng</THDon>
            <KHMSHDon>1</KHMSHDon>
            <KHHDon>C25TAA</KHHDon>
            <SHDon>00003656</SHDon>
            <NLap>2025-09-15</NLap>
            <DVTTe>VND</DVTTe>
            <TGia>1</TGia>
            <HTTToan>Tiền mặt/Chuyển khoản</HTTToan>
            <MSTTCGP>0101360697</MSTTCGP>
        </TTChung>
        <MCCQT Id="Id-23fd9318f1a04c3a9a106c5cea19ec31">PL536N267XA</MCCQT>
    </DLHDon>
</HDon>`;
            document.getElementById('xmlInputBkav').value = sampleXML;
            validateXMLBkav();
        }
        
        // Xóa dữ liệu cho tab BKAV
        function clearInputBkav() {
            document.getElementById('xmlInputBkav').value = '';
            document.getElementById('fileInputBkav').value = '';
            document.getElementById('resultContainerBkav').style.display = 'none';
            document.getElementById('validationMessageBkav').style.display = 'none';
            document.getElementById('generateBtnBkav').disabled = true;
            BKAV_MODULE.isAuthenticated = false;
        }

        // Cho phép kéo thả file cho tab BKAV
        document.addEventListener('DOMContentLoaded', function() {
            const fileUpload = document.querySelector('.file-upload-bkav-input');
            
            fileUpload.addEventListener('dragover', function(e) {
                e.preventDefault();
                this.style.background = '#e3f2fd';
            });
            
            fileUpload.addEventListener('dragleave', function(e) {
                e.preventDefault();
                this.style.background = '';
            });
            
            fileUpload.addEventListener('drop', function(e) {
                e.preventDefault();
                this.style.background = '';
                const files = e.dataTransfer.files;
                handleFileUploadBkav(files);
            });
        });

        // Tự động load XML mẫu khi tab BKAV được tải
        document.getElementById('module-bkav-tab').addEventListener('click', function() {
            if (document.getElementById('xmlInputBkav').value === '') {
                loadSampleXMLBkav();
            }
        });

        // ==============================================
        // PHẦN XỬ LÝ CHO TAB 4: TRA CỨU HÓA ĐƠN - FIX LỖI
        // ==============================================

        // Mở modal tra cứu - ĐÃ ĐƯỢC SỬA
        document.getElementById('openLookupModalBtn').addEventListener('click', function() {
            // Kiểm tra xem có dữ liệu hóa đơn từ XML không
            const jsonData = loadJsonFromStorage();
            if (!jsonData || !jsonData.processedData) {
                alert('Vui lòng tải lên file XML hóa đơn trước khi sử dụng chức năng tra cứu!');
                return;
            }
            
            // Cập nhật dữ liệu từ JSON - YÊU CẦU 1 & 2
            invoiceData = jsonData.processedData;
            matchedProviders = findMatchingProviders(invoiceData.serviceProviderTaxCode);
            
            // Khởi tạo danh sách nhà cung cấp dựa trên MSTTCGP - YÊU CẦU 2
            initializeProviders();
            
            // HIỂN THỊ THÔNG TIN HÓA ĐƠN TỪ XML - ĐÃ ĐƯỢC THÊM - YÊU CẦU 6
            displayInvoiceInfoInModal();
            
            // Hiển thị mã tra cứu nếu có
            if (invoiceData.verificationCode) {
                document.getElementById('lookupCode').value = invoiceData.verificationCode;
            }
            
            // Hiển thị danh sách nhà cung cấp
            displayProviderList();
            
            // Reset trạng thái
            document.getElementById('directPdfInfo').style.display = 'none';
            document.getElementById('lookupCodeGroup').style.display = 'block';
            document.getElementById('lookupResult').style.display = 'none';
            document.getElementById('directLookupSection').style.display = 'none';
            
            document.getElementById('lookupModal').style.display = 'block';
        });

        // HÀM HIỂN THỊ THÔNG TIN HÓA ĐƠN TRONG MODAL - ĐÃ ĐƯỢC THÊM - YÊU CẦU 6
        function displayInvoiceInfoInModal() {
            const invoiceInfoContainer = document.getElementById('invoiceInfoFromXml');
            const invoiceInfoDetails = document.getElementById('invoiceInfoDetails');
            
            if (!invoiceData.sellerTaxCode) {
                invoiceInfoContainer.style.display = 'none';
                return;
            }
            
            const infoHtml = `
                <div class="info-item">
                    <span class="info-label">Đơn vị bán hàng:</span>
                    <span class="info-value">${invoiceData.sellerName || 'N/A'}</span>
                </div>
                <div class="info-item">
                    <span class="info-label">MST người bán:</span>
                    <span class="info-value">${invoiceData.sellerTaxCode || 'N/A'}</span>
                </div>
                <div class="info-item">
                    <span class="info-label">Mã tra cứu:</span>
                    <span class="info-value">${invoiceData.verificationCode || 'N/A'}</span>
                </div>
                ${invoiceData.portalCodes && invoiceData.portalCodes.length > 0 ? `
                <div class="info-item">
                    <span class="info-label">Các mã tìm thấy:</span>
                    <span class="info-value">
                        ${invoiceData.portalCodes.map(code => `${code.code} (${code.type})`).join(', ')}
                    </span>
                </div>
                ` : ''}
            `;
            
            invoiceInfoDetails.innerHTML = infoHtml;
            invoiceInfoContainer.style.display = 'block';
        }

        // Xử lý sự kiện cho tab trong modal tra cứu
        document.querySelectorAll('#lookupModal .tab').forEach(tab => {
            tab.addEventListener('click', function() {
                // Xóa active class từ tất cả các tab và nội dung
                document.querySelectorAll('#lookupModal .tab').forEach(t => t.classList.remove('active'));
                document.querySelectorAll('#lookupModal .tab-content').forEach(c => c.classList.remove('active'));
                
                // Thêm active class cho tab được chọn
                this.classList.add('active');
                
                // Hiển thị nội dung tương ứng
                const tabId = this.getAttribute('data-tab');
                document.getElementById(tabId).classList.add('active');
            });
        });

        // Tìm kiếm nhà cung cấp
        document.getElementById('providerSearch').addEventListener('input', function() {
            const searchTerm = this.value.toLowerCase();
            const rows = document.querySelectorAll('.provider-table tbody tr');
            
            rows.forEach(row => {
                const text = row.textContent.toLowerCase();
                if (text.includes(searchTerm)) {
                    row.style.display = '';
                } else {
                    row.style.display = 'none';
                }
            });
        });

        // Xử lý khi chọn nhà cung cấp trong dropdown - ĐÃ ĐƯỢC SỬA
        document.getElementById('providerSelect').addEventListener('change', function() {
            const selectedValue = this.value;
            const selectedOption = this.options[this.selectedIndex];
            const providerMST = selectedValue;
            const lookupCode = document.getElementById('lookupCode').value.trim() || invoiceData.verificationCode;
            const transactionID = invoiceData.transactionID;
            const sellerTaxCode = invoiceData.sellerTaxCode;
            
            // YÊU CẦU 3: Nếu MSTTCGP trùng với module thì lấy link từ module
            let pdfDownloadInfo = null;
            
            if (providerMST === MISA_MODULE.mst) {
                // Module MISA
                pdfDownloadInfo = {
                    provider: MISA_MODULE,
                    pdfUrl: `${MISA_MODULE.pdfGeneratorUrl}?Type=pdf&MST=${sellerTaxCode}&Code=${transactionID}&Module=${MISA_MODULE.mst}`,
                    requiresCode: false,
                    autoFillSupported: true
                };
            } else if (providerMST === BKAV_MODULE.id) {
                // Module BKAV
                const bkavResult = BKAV_MODULE.generateFromXML(invoiceData.xmlContent);
                if (bkavResult.success) {
                    pdfDownloadInfo = {
                        provider: BKAV_MODULE,
                        pdfUrl: bkavResult.data.downloadLink,
                        requiresCode: false,
                        autoFillSupported: true
                    };
                }
            } else if (providerMST === VIENH_HY_MODULE.mst) {
                // Module Vĩnh Hy
                const formattedId = formatInvoiceIdForLink(transactionID, sellerTaxCode);
                pdfDownloadInfo = {
                    provider: VIENH_HY_MODULE,
                    pdfUrl: `${VIENH_HY_MODULE.pdfGeneratorUrl}?r=${formattedId}&type=pdf`,
                    requiresCode: false,
                    autoFillSupported: true
                };
            } else if (providerMST === EASYINVOICE_MODULE.mst) {
                // Module EasyInvoice
                const jsonData = loadJsonFromStorage();
                if (jsonData) {
                    const easyInvoiceResult = createEasyInvoiceLookupURL(jsonData);
                    if (easyInvoiceResult.success) {
                        pdfDownloadInfo = {
                            provider: EASYINVOICE_MODULE,
                            pdfUrl: easyInvoiceResult.lookupUrl,
                            requiresCode: false,
                            autoFillSupported: true
                        };
                    }
                }
            } else {
                // Các nhà cung cấp khác - YÊU CẦU 4
                pdfDownloadInfo = checkDirectPdfDownloadSupport(providerMST, lookupCode);
            }
            
            // Kiểm tra URL tra cứu đặc biệt
            const specialLookupUrl = createSpecialLookupUrl(providerMST, transactionID, sellerTaxCode, lookupCode);
            
            // Tạo URL tự động điền
            const autoFillUrl = createAutoFillUrl(providerMST, sellerTaxCode, lookupCode, transactionID);
            
            const directPdfInfo = document.getElementById('directPdfInfo');
            
            if (pdfDownloadInfo || specialLookupUrl || autoFillUrl) {
                let infoContent = '';
                
                if (pdfDownloadInfo && pdfDownloadInfo.pdfUrl) {
                    // Có thể tải trực tiếp
                    infoContent += `
                        <h4>🎉 Có thể tải PDF trực tiếp!</h4>
                        <p>Nhà cung cấp <strong>${pdfDownloadInfo.provider.name}</strong> hỗ trợ tải hóa đơn PDF trực tiếp.</p>
                        <p>${pdfDownloadInfo.provider.description || 'Hỗ trợ tải PDF từ nhà cung cấp'}</p>
                        <div style="text-align: center; margin-top: 15px;">
                            <a href="${pdfDownloadInfo.pdfUrl}" target="_blank" class="direct-pdf-link">
                                📥 Tải hóa đơn PDF từ ${pdfDownloadInfo.provider.name}
                            </a>
                        </div>
                    `;
                }
                
                if (specialLookupUrl) {
                    infoContent += `
                        <h4>🔗 Liên kết tra cứu đặc biệt</h4>
                        <p>Nhà cung cấp <strong>${selectedOption.getAttribute('data-provider-name')}</strong> có liên kết tra cứu đặc biệt.</p>
                        <div style="text-align: center; margin-top: 15px;">
                            <a href="${specialLookupUrl}" target="_blank" class="direct-pdf-link">
                                🔍 Truy cập trang tra cứu ${selectedOption.getAttribute('data-provider-name')}
                            </a>
                        </div>
                    `;
                }
                
                if (autoFillUrl && pdfDownloadInfo && pdfDownloadInfo.autoFillSupported) {
                    infoContent += `
                        <h4>🤖 Liên kết tự động điền</h4>
                        <p>Đã tự động điền thông tin MST người bán và mã tra cứu (nếu có).</p>
                        <div style="text-align: center; margin-top: 15px;">
                            <a href="${autoFillUrl}" target="_blank" class="direct-pdf-link">
                                🚀 Mở trang với thông tin đã điền
                            </a>
                        </div>
                        <p style="margin-top: 10px; font-size: 12px; color: #666;">
                            <em>MST người bán: ${sellerTaxCode || 'Không có'} | Mã tra cứu: ${lookupCode || 'Không có'}</em>
                        </p>
                    `;
                } else if (autoFillUrl) {
                    infoContent += `
                        <h4>🔗 Liên kết tra cứu</h4>
                        <p>Truy cập trang tra cứu của nhà cung cấp.</p>
                        <div style="text-align: center; margin-top: 15px;">
                            <a href="${autoFillUrl}" target="_blank" class="direct-pdf-link">
                                🔍 Mở trang tra cứu ${selectedOption.getAttribute('data-provider-name')}
                            </a>
                        </div>
                    `;
                }
                
                if (pdfDownloadInfo && pdfDownloadInfo.requiresCode && !lookupCode) {
                    infoContent += `
                        <div style="background: #fff3cd; padding: 15px; border-radius: 8px; margin-top: 15px;">
                            <h4>ℹ️ Cần mã tra cứu</h4>
                            <p>Nhà cung cấp <strong>${pdfDownloadInfo.provider.name}</strong> hỗ trợ tải PDF trực tiếp nhưng yêu cầu mã tra cứu.</p>
                        </div>
                    `;
                }
                
                infoContent += `<p style="margin-top: 10px; font-size: 12px; color: #666;">
                    <em>Liên kết này sẽ mở trong tab mới</em>
                </p>`;
                
                directPdfInfo.innerHTML = infoContent;
                directPdfInfo.style.display = 'block';
                
                // Ẩn ô nhập mã tra cứu nếu đã có đủ thông tin
                if ((pdfDownloadInfo && pdfDownloadInfo.pdfUrl) || specialLookupUrl || autoFillUrl) {
                    document.getElementById('lookupCodeGroup').style.display = 'none';
                } else {
                    document.getElementById('lookupCodeGroup').style.display = 'block';
                }
            } else {
                // Không hỗ trợ tải trực tiếp
                directPdfInfo.style.display = 'none';
                document.getElementById('lookupCodeGroup').style.display = 'block';
            }
            
            // Lưu thông tin nhà cung cấp hiện tại
            if (selectedOption) {
                currentSelectedProvider = {
                    mst: providerMST,
                    name: selectedOption.getAttribute('data-provider-name'),
                    url: selectedOption.getAttribute('data-url')
                };
            }
        });

        // Thực hiện tra cứu - ĐÃ ĐƯỢC SỬA
        document.getElementById('performLookupBtn').addEventListener('click', function() {
            const providerSelect = document.getElementById('providerSelect');
            const selectedOption = providerSelect.options[providerSelect.selectedIndex];
            const providerMST = providerSelect.value;
            const providerName = selectedOption.getAttribute('data-provider-name');
            const providerUrl = selectedOption.getAttribute('data-url');
            const lookupCode = document.getElementById('lookupCode').value.trim();
            const transactionID = invoiceData.transactionID;
            const sellerTaxCode = invoiceData.sellerTaxCode;
            const lookupResult = document.getElementById('lookupResult');
            const providerDetails = document.getElementById('providerDetails');
            const directLookupSection = document.getElementById('directLookupSection');
            
            if (!providerMST) {
                lookupResult.textContent = "Vui lòng chọn nhà cung cấp!";
                lookupResult.className = 'lookup-result lookup-error';
                lookupResult.style.display = 'block';
                return;
            }
            
            // YÊU CẦU 3: Nếu MSTTCGP trùng với module thì lấy link từ module
            let pdfDownloadInfo = null;
            
            if (providerMST === MISA_MODULE.mst) {
                // Module MISA
                pdfDownloadInfo = {
                    provider: MISA_MODULE,
                    pdfUrl: `${MISA_MODULE.pdfGeneratorUrl}?Type=pdf&MST=${sellerTaxCode}&Code=${transactionID}&Module=${MISA_MODULE.mst}`,
                    requiresCode: false,
                    autoFillSupported: true
                };
            } else if (providerMST === BKAV_MODULE.id) {
                // Module BKAV
                const bkavResult = BKAV_MODULE.generateFromXML(invoiceData.xmlContent);
                if (bkavResult.success) {
                    pdfDownloadInfo = {
                        provider: BKAV_MODULE,
                        pdfUrl: bkavResult.data.downloadLink,
                        requiresCode: false,
                        autoFillSupported: true
                    };
                }
            } else if (providerMST === VIENH_HY_MODULE.mst) {
                // Module Vĩnh Hy
                const formattedId = formatInvoiceIdForLink(transactionID, sellerTaxCode);
                pdfDownloadInfo = {
                    provider: VIENH_HY_MODULE,
                    pdfUrl: `${VIENH_HY_MODULE.pdfGeneratorUrl}?r=${formattedId}&type=pdf`,
                    requiresCode: false,
                    autoFillSupported: true
                };
            } else if (providerMST === EASYINVOICE_MODULE.mst) {
                // Module EasyInvoice
                const jsonData = loadJsonFromStorage();
                if (jsonData) {
                    const easyInvoiceResult = createEasyInvoiceLookupURL(jsonData);
                    if (easyInvoiceResult.success) {
                        pdfDownloadInfo = {
                            provider: EASYINVOICE_MODULE,
                            pdfUrl: easyInvoiceResult.lookupUrl,
                            requiresCode: false,
                            autoFillSupported: true
                        };
                    }
                }
            } else {
                // Các nhà cung cấp khác - YÊU CẦU 4
                pdfDownloadInfo = checkDirectPdfDownloadSupport(providerMST, lookupCode);
            }
            
            // Kiểm tra URL tra cứu đặc biệt
            const specialLookupUrl = createSpecialLookupUrl(providerMST, transactionID, sellerTaxCode, lookupCode);
            
            // Tạo URL tự động điền
            const autoFillUrl = createAutoFillUrl(providerMST, sellerTaxCode, lookupCode, transactionID);
            
            // Hiển thị thông tin nhà cung cấp
            providerDetails.innerHTML = `
                <h4>Thông tin nhà cung cấp giải pháp:</h4>
                <p><strong>Tên đơn vị:</strong> ${providerName}</p>
                <p><strong>Mã số thuế:</strong> ${providerMST}</p>
                <p><strong>URL tra cứu:</strong> <a href="${providerUrl}" target="_blank">${providerUrl}</a></p>
            `;
            providerDetails.style.display = 'block';
            
            // Tạo các liên kết tra cứu trực tiếp
            let directLookupLinks = '';
            
            if (pdfDownloadInfo && pdfDownloadInfo.pdfUrl) {
                directLookupLinks += `<a href="${pdfDownloadInfo.pdfUrl}" target="_blank" class="direct-lookup-btn">📥 Tải PDF trực tiếp</a>`;
            }
            
            if (specialLookupUrl) {
                directLookupLinks += `<a href="${specialLookupUrl}" target="_blank" class="direct-lookup-btn">🔗 Tra cứu đặc biệt</a>`;
            }
            
            if (autoFillUrl) {
                if (pdfDownloadInfo && pdfDownloadInfo.autoFillSupported) {
                    directLookupLinks += `<a href="${autoFillUrl}" target="_blank" class="direct-lookup-btn">🚀 Tự động điền thông tin</a>`;
                } else {
                    directLookupLinks += `<a href="${autoFillUrl}" target="_blank" class="direct-lookup-btn">🔍 Mở trang tra cứu</a>`;
                }
            }
            
            // Thêm liên kết đến trang tra cứu của nhà cung cấp
            if (!autoFillUrl) {
                directLookupLinks += `<a href="${providerUrl}" target="_blank" class="direct-lookup-btn">🔍 Mở trang tra cứu</a>`;
            }
            
            // Hiển thị phần tra cứu trực tiếp
            directLookupSection.innerHTML = `
                <h4>Tra cứu trực tiếp:</h4>
                <p>Nhấn vào liên kết dưới đây để mở trang tra cứu hoặc tải PDF trực tiếp:</p>
                <div style="display: flex; flex-wrap: wrap; gap: 10px; justify-content: center;">${directLookupLinks}</div>
            `;
            directLookupSection.style.display = 'block';
            
            // Hiển thị kết quả tra cứu
            let resultMessage = '';
            
            if (pdfDownloadInfo && pdfDownloadInfo.pdfUrl) {
                resultMessage = `
                    <h4>✅ Có thể tải PDF trực tiếp!</h4>
                    <p>Mã tra cứu của bạn: <strong>${lookupCode || 'Không cần mã'}</strong></p>
                    <p>MST người bán: <strong>${sellerTaxCode || 'Không có'}</strong></p>
                    <p>Nhà cung cấp: <strong>${providerName}</strong></p>
                    <p>Bạn có thể tải PDF trực tiếp bằng liên kết bên trên.</p>
                `;
                
                // Cập nhật link tra cứu ở footer
                updateFooterLookupLink(pdfDownloadInfo.pdfUrl, providerName);
            } else if (specialLookupUrl) {
                resultMessage = `
                    <h4>🔗 Liên kết tra cứu đặc biệt</h4>
                    <p>Transaction ID: <strong>${transactionID || 'Không có'}</strong></p>
                    <p>MST người bán: <strong>${sellerTaxCode || 'Không có'}</strong></p>
                    <p>Nhà cung cấp: <strong>${providerName}</strong></p>
                    <p>Đã tạo liên kết tra cứu đặc biệt cho nhà cung cấp này.</p>
                `;
                
                // Cập nhật link tra cứu ở footer
                updateFooterLookupLink(specialLookupUrl, providerName);
            } else if (autoFillUrl) {
                resultMessage = `
                    <h4>🤖 Đã tạo liên kết tự động điền</h4>
                    <p>MST người bán: <strong>${sellerTaxCode || 'Không có'}</strong></p>
                    <p>Mã tra cứu: <strong>${lookupCode || 'Không có'}</strong></p>
                    <p>Nhà cung cấp: <strong>${providerName}</strong></p>
                    <p>Liên kết đã tự động điền thông tin vào trang tra cứu.</p>
                `;
                
                // Cập nhật link tra cứu ở footer
                updateFooterLookupLink(autoFillUrl, providerName);
            } else if (lookupCode) {
                resultMessage = `
                    <h4>Thông tin tra cứu:</h4>
                    <p>Mã tra cứu của bạn: <strong>${lookupCode}</strong></p>
                    <p>MST người bán: <strong>${sellerTaxCode || 'Không có'}</strong></p>
                    <p>Nhà cung cấp: <strong>${providerName}</strong></p>
                    <p>Vui lòng nhập mã này vào trang tra cứu của nhà cung cấp để tải hóa đơn PDF.</p>
                `;
            } else {
                resultMessage = `
                    <h4>Thông tin nhà cung cấp:</h4>
                    <p>MST người bán: <strong>${sellerTaxCode || 'Không có'}</strong></p>
                    <p>Nhà cung cấp: <strong>${providerName}</strong></p>
                    <p>Một số nhà cung cấp không yêu cầu mã tra cứu. Vui lòng thử truy cập trang tra cứu trực tiếp.</p>
                `;
            }
            
            lookupResult.innerHTML = resultMessage;
            lookupResult.className = 'lookup-result lookup-success';
            lookupResult.style.display = 'block';
        });

        // Đóng modal tra cứu
        document.getElementById('closeLookupModalBtn').addEventListener('click', closeLookupModal);
        document.getElementById('closeLookupModalBtn2').addEventListener('click', closeLookupModal);
        
        function closeLookupModal() {
            document.getElementById('lookupModal').style.display = 'none';
            document.getElementById('lookupResult').style.display = 'none';
            document.getElementById('lookupResult').className = 'lookup-result';
            document.getElementById('providerDetails').style.display = 'none';
            document.getElementById('directLookupSection').style.display = 'none';
            document.getElementById('directPdfInfo').style.display = 'none';
            document.getElementById('lookupCodeGroup').style.display = 'block';
        }

        // ==============================================
        // PHẦN XỬ LÝ CHO TAB 5: HÓA ĐƠN GTGT - CẢI THIỆN
        // ==============================================

        // Biến lưu trữ dữ liệu hóa đơn GTGT
        let invoiceDataGtgt = {};
        let currentPageGtgt = 1;
        let totalPagesGtgt = 1;
        let paginatedInvoiceDataGtgt = [];
        let maxLinesPerPageGtgt = 10; // Số dòng mặc định mỗi trang

        // Xử lý sự kiện khi chọn file cho tab GTGT
        document.getElementById('xmlFileGtgt').addEventListener('change', function(e) {
            const fileName = e.target.files[0] ? e.target.files[0].name : 'Chưa chọn file';
            document.getElementById('fileNameGtgt').textContent = `File đã chọn: ${fileName}`;
        });

        // Xử lý tải lên file XML cho tab GTGT
        document.getElementById('uploadBtnGtgt').addEventListener('click', function() {
            const fileInput = document.getElementById('xmlFileGtgt');
            const file = fileInput.files[0];
            
            if (!file) {
                alert('Vui lòng chọn file XML trước khi tải lên!');
                return;
            }
            
            document.getElementById('loadingGtgt').style.display = 'block';
            document.getElementById('invoicePreviewGtgt').style.display = 'none';
            document.getElementById('requestOriginalBtn').style.display = 'none';
            
            const reader = new FileReader();
            reader.onload = function(e) {
                setTimeout(() => {
                    const xmlText = e.target.result;
                    
                    // Xử lý XML và lưu vào JSON - YÊU CẦU 1 & 8
                    const processedData = processXmlData(xmlText);
                    
                    if (processedData) {
                        // Xử lý dữ liệu XML cho hóa đơn GTGT
                        const success = processXmlDataGtgtForDisplay(xmlText);
                        
                        if (success) {
                            // Phân trang dữ liệu
                            paginateInvoiceDataGtgt();
                            // Hiển thị trang đầu tiên
                            showPageGtgt(1);
                            
                            document.getElementById('loadingGtgt').style.display = 'none';
                            document.getElementById('invoicePreviewGtgt').style.display = 'block';
                            document.getElementById('requestOriginalBtn').style.display = 'inline-block';
                        } else {
                            document.getElementById('loadingGtgt').style.display = 'none';
                        }
                    } else {
                        document.getElementById('loadingGtgt').style.display = 'none';
                        alert('Có lỗi xảy ra khi xử lý file XML.');
                    }
                }, 500);
            };
            
            reader.onerror = function() {
                alert('Lỗi khi đọc file. Vui lòng thử lại.');
                document.getElementById('loadingGtgt').style.display = 'none';
            };
            
            reader.readAsText(file);
        });

        // Hàm xử lý dữ liệu XML cho tab GTGT (hiển thị)
        function processXmlDataGtgtForDisplay(xmlText) {
            try {
                const parser = new DOMParser();
                const xmlDoc = parser.parseFromString(xmlText, 'text/xml');
                
                // Kiểm tra lỗi XML
                const parseError = xmlDoc.getElementsByTagName('parsererror');
                if (parseError.length > 0) {
                    alert('File XML không hợp lệ. Vui lòng kiểm tra lại file.');
                    return false;
                }
                
                return processXmlDataGtgt(xmlDoc);
            } catch (error) {
                console.error('Lỗi khi xử lý XML:', error);
                alert('Có lỗi xảy ra khi xử lý file XML. Vui lòng kiểm tra lại file.');
                return false;
            }
        }

        // Hàm xử lý dữ liệu XML cho tab GTGT
        function processXmlDataGtgt(xmlDoc) {
            try {
                // Reset dữ liệu
                invoiceDataGtgt = {};
                
                // Lấy thông tin chung
                const ttChung = xmlDoc.getElementsByTagName('TTChung')[0];
                if (!ttChung) {
                    alert('Không tìm thấy thông tin chung của hóa đơn');
                    return false;
                }
                
                invoiceDataGtgt.invoiceDate = formatDateGtgt(getElementTextGtgt(ttChung, 'NLap'));
                invoiceDataGtgt.serialNo = getElementTextGtgt(ttChung, 'KHHDon');
                invoiceDataGtgt.invoiceNo = getElementTextGtgt(ttChung, 'SHDon');
                invoiceDataGtgt.exchangeRate = getElementTextGtgt(ttChung, 'TGia', '1');
                invoiceDataGtgt.paymentMethod = getElementTextGtgt(ttChung, 'HTTToan', 'Tiền mặt');
                
                // Lấy thông tin người bán
                const nBan = xmlDoc.getElementsByTagName('NBan')[0];
                if (nBan) {
                    invoiceDataGtgt.sellerName = getElementTextGtgt(nBan, 'Ten');
                    invoiceDataGtgt.sellerTaxCode = getElementTextGtgt(nBan, 'MST');
                    invoiceDataGtgt.sellerAddress = getElementTextGtgt(nBan, 'DChi');
                }
                
                // Lấy thông tin người mua
                const nMua = xmlDoc.getElementsByTagName('NMua')[0];
                if (nMua) {
                    invoiceDataGtgt.buyerName = getElementTextGtgt(nMua, 'Ten');
                    invoiceDataGtgt.buyerTaxCode = getElementTextGtgt(nMua, 'MST');
                    invoiceDataGtgt.buyerAddress = getElementTextGtgt(nMua, 'DChi');
                }
                
                // Lấy thông tin hàng hóa/dịch vụ
                const dshhdvu = xmlDoc.getElementsByTagName('DSHHDVu')[0];
                invoiceDataGtgt.products = [];
                
                if (dshhdvu) {
                    const products = dshhdvu.getElementsByTagName('HHDVu');
                    for (let i = 0; i < products.length; i++) {
                        const product = products[i];
                        const thueSuat = getElementTextGtgt(product, 'TSuat', '0');
                        const tienThue = getElementTextGtgt(product, 'TThue', '0');
                        
                        invoiceDataGtgt.products.push({
                            stt: getElementTextGtgt(product, 'STT', (i + 1).toString()),
                            description: getElementTextGtgt(product, 'THHDVu'),
                            unit: getElementTextGtgt(product, 'DVTinh', ''),
                            quantity: getElementTextGtgt(product, 'SLuong', '1'),
                            unitPrice: getElementTextGtgt(product, 'DGia', '0'),
                            amount: getElementTextGtgt(product, 'ThTien', '0'),
                            taxRate: thueSuat,
                            taxAmount: tienThue
                        });
                    }
                }
                
                // Lấy thông tin tổng thanh toán
                const tToan = xmlDoc.getElementsByTagName('TToan')[0];
                if (tToan) {
                    invoiceDataGtgt.totalAmount = getElementTextGtgt(tToan, 'TgTCThue', '0');
                    invoiceDataGtgt.vatAmount = getElementTextGtgt(tToan, 'TgTThue', '0');
                    invoiceDataGtgt.totalPayment = getElementTextGtgt(tToan, 'TgTTTBSo', '0');
                    invoiceDataGtgt.amountInWords = getElementTextGtgt(tToan, 'TgTTTBChu', '');
                }
                
                // Lấy thông tin mã tra cứu và mã cơ quan thuế
                const mccqt = xmlDoc.getElementsByTagName('MCCQT')[0];
                if (mccqt) {
                    invoiceDataGtgt.verificationCode = mccqt.textContent || '';
                    invoiceDataGtgt.taxAuthorityCode = mccqt.getAttribute('Id') || '';
                }
                
                // Lấy thông tin transaction ID từ DLHDon
                const dlhdon = xmlDoc.getElementsByTagName('DLHDon')[0];
                if (dlhdon) {
                    invoiceDataGtgt.transactionId = dlhdon.getAttribute('Id') || '';
                }
                
                return true;
            } catch (error) {
                console.error('Lỗi khi xử lý XML:', error);
                alert('Có lỗi xảy ra khi xử lý file XML. Vui lòng kiểm tra lại file.');
                return false;
            }
        }

        // Hàm phân trang dữ liệu hóa đơn cho tab GTGT
        function paginateInvoiceDataGtgt() {
            paginatedInvoiceDataGtgt = [];
            const allProducts = [...invoiceDataGtgt.products];
            
            // Tính toán số dòng tối đa mỗi trang dựa trên chiều cao tên sản phẩm
            maxLinesPerPageGtgt = calculateMaxLinesPerPageGtgt(allProducts);
            
            // Nếu số sản phẩm ít hơn hoặc bằng maxLinesPerPage, tất cả sẽ ở trang cuối
            if (allProducts.length <= maxLinesPerPageGtgt) {
                paginatedInvoiceDataGtgt.push({
                    products: allProducts,
                    isLastPage: true,
                    pageNumber: 1,
                    totalPages: 1
                });
                totalPagesGtgt = 1;
                return;
            }
            
            // Phân trang
            let pageNumber = 1;
            let remainingProducts = [...allProducts];
            
            while (remainingProducts.length > 0) {
                const isLastPage = remainingProducts.length <= maxLinesPerPageGtgt;
                const productsForPage = remainingProducts.splice(0, isLastPage ? remainingProducts.length : maxLinesPerPageGtgt);
                
                paginatedInvoiceDataGtgt.push({
                    products: productsForPage,
                    isLastPage: isLastPage,
                    pageNumber: pageNumber,
                    totalPages: Math.ceil(allProducts.length / maxLinesPerPageGtgt)
                });
                
                pageNumber++;
            }
            
            totalPagesGtgt = paginatedInvoiceDataGtgt.length;
            
            // Cập nhật totalPages cho tất cả các trang
            paginatedInvoiceDataGtgt.forEach(page => {
                page.totalPages = totalPagesGtgt;
            });
        }

        // Hàm tính toán số dòng tối đa mỗi trang cho tab GTGT - CẢI THIỆN
        function calculateMaxLinesPerPageGtgt(products) {
            // Chiều cao cố định của các phần header và footer
            const headerHeight = 350; // Chiều cao ước tính của header (px)
            const footerHeight = 150; // Chiều cao ước tính của footer (px)
            const signatureHeight = 120; // Chiều cao phần chữ ký (chỉ ở trang cuối)
            const pageHeight = 297 * 3.78; // Chiều cao A4 tính bằng px (297mm * 3.78px/mm)
            
            // Tính chiều cao trung bình của mỗi dòng sản phẩm
            let totalProductHeight = 0;
            let productCount = 0;
            
            products.forEach(product => {
                // Ước tính chiều cao dựa trên độ dài tên sản phẩm
                const descriptionLength = product.description ? product.description.length : 0;
                // Mỗi dòng chứa khoảng 50 ký tự, mỗi dòng cao 20px
                const lines = Math.ceil(descriptionLength / 50);
                const productHeight = lines * 20;
                totalProductHeight += productHeight;
                productCount++;
            });
            
            const avgProductHeight = productCount > 0 ? totalProductHeight / productCount : 30;
            
            // Tính số dòng tối đa có thể chứa trong một trang
            const availableHeight = pageHeight - headerHeight - footerHeight;
            const maxLines = Math.floor(availableHeight / avgProductHeight);
            
            // Đảm bảo ít nhất 5 dòng và tối đa 15 dòng mỗi trang
            return Math.max(5, Math.min(15, maxLines));
        }

        // Hàm hiển thị trang cụ thể cho tab GTGT
        function showPageGtgt(pageNumber) {
            if (pageNumber < 1 || pageNumber > totalPagesGtgt) return;
            
            currentPageGtgt = pageNumber;
            const pageData = paginatedInvoiceDataGtgt[pageNumber - 1];
            
            // Cập nhật thông tin trang
            document.getElementById('pageInfo').textContent = `Trang ${pageNumber} / ${totalPagesGtgt}`;
            
            // Cập nhật trạng thái nút điều hướng
            document.getElementById('prevPageBtn').disabled = pageNumber === 1;
            document.getElementById('nextPageBtn').disabled = pageNumber === totalPagesGtgt;
            
            // Tạo nội dung cho trang hiện tại
            const previewContent = document.getElementById('previewContentGtgt');
            const invoiceHtml = generateGtgtInvoicePage(pageData);
            previewContent.innerHTML = invoiceHtml;
        }

        // Hàm lấy text từ element cho tab GTGT
        function getElementTextGtgt(parentElement, tagName, defaultValue = '') {
            const elements = parentElement.getElementsByTagName(tagName);
            return elements.length > 0 ? elements[0].textContent : defaultValue;
        }

        // Hàm định dạng ngày cho tab GTGT
        function formatDateGtgt(dateString) {
            if (!dateString) return '';
            try {
                const date = new Date(dateString);
                const day = date.getDate().toString().padStart(2, '0');
                const month = (date.getMonth() + 1).toString().padStart(2, '0');
                const year = date.getFullYear();
                return `${day} tháng ${month} năm ${year}`;
            } catch (e) {
                return dateString;
            }
        }

        // Hàm định dạng số cho tab GTGT - LÀM TRÒN CHỈ LẤY PHẦN NGUYÊN
        function formatNumberGtgt(numberStr) {
            if (!numberStr) return '';
            const number = parseFloat(numberStr.replace(/[^\d.]/g, ''));
            if (isNaN(number)) return '';
            // Làm tròn chỉ lấy phần nguyên
            return Math.round(number).toLocaleString('vi-VN');
        }

        // Hàm định dạng số cho tab GTGT - Nếu bằng 0 thì không hiển thị
        function formatNumberGtgtZero(numberStr) {
            if (!numberStr) return '';
            const number = parseFloat(numberStr.replace(/[^\d.]/g, ''));
            if (isNaN(number) || number === 0) return '';
            // Làm tròn chỉ lấy phần nguyên
            return Math.round(number).toLocaleString('vi-VN');
        }

        // Hàm định dạng thuế suất
        function formatTaxRateGtgt(taxRateStr) {
            if (!taxRateStr) return '';
            const taxRate = parseFloat(taxRateStr);
            if (isNaN(taxRate) || taxRate === 0) return '';
            return taxRate + '%';
        }

        // Tạo nội dung hóa đơn GTGT cho một trang cụ thể - CẢI THIỆN
        function generateGtgtInvoicePage(pageData) {
            const isLastPage = pageData.isLastPage;
            const pageNumber = pageData.pageNumber;
            const totalPages = pageData.totalPages;
            
            // Tạo danh sách sản phẩm cho trang này
            let productsHtml = '';
            const productsForPage = pageData.products;
            
            // Thêm các dòng sản phẩm thực tế
            productsForPage.forEach((product, index) => {
                productsHtml += `
                    <tr>
                        <td align="center" class="invoice-gtgt-item-normal">${product.stt}</td>
                        <td align="left" class="invoice-gtgt-item-normal">${product.description}</td>
                        <td align="center" class="invoice-gtgt-item-normal">${product.unit}</td>
                        <td align="right" class="invoice-gtgt-item-normal">${formatNumberGtgtZero(product.quantity)}</td>
                        <td align="right" class="invoice-gtgt-item-normal">${formatNumberGtgtZero(product.unitPrice)}</td>
                        <td align="right" class="invoice-gtgt-item-normal">${formatNumberGtgtZero(product.amount)}</td>
                    </tr>
                `;
            });
            
            // Nếu là trang đầu tiên và số dòng ít hơn maxLinesPerPage, thêm các dòng trống
            if (pageNumber === 1 && productsForPage.length < maxLinesPerPageGtgt) {
                const emptyRows = maxLinesPerPageGtgt - productsForPage.length;
                for (let i = 0; i < emptyRows; i++) {
                    productsHtml += `
                        <tr>
                            <td align="center" class="invoice-gtgt-item-normal">&nbsp;</td>
                            <td align="left" class="invoice-gtgt-item-normal">&nbsp;</td>
                            <td align="center" class="invoice-gtgt-item-normal">&nbsp;</td>
                            <td align="right" class="invoice-gtgt-item-normal">&nbsp;</td>
                            <td align="right" class="invoice-gtgt-item-normal">&nbsp;</td>
                            <td align="right" class="invoice-gtgt-item-normal">&nbsp;</td>
                        </tr>
                    `;
                }
            }
            
            // Lấy thông tin nhà cung cấp dịch vụ từ XML
            const serviceProviderTaxCode = invoiceDataGtgt.serviceProviderTaxCode || invoiceData.serviceProviderTaxCode || '';
            const serviceProviderName = getServiceProviderName(serviceProviderTaxCode);
            
            // Lấy mã tra cứu từ kết quả hàm extractPortalCodes
            const portalCodes = invoiceData.portalCodes || [];
            const verificationCode = portalCodes.length > 0 ? portalCodes[0].code : invoiceDataGtgt.verificationCode || '';
            
            return `
                <div class="invoice-gtgt-container force-times-new-roman">
                    <table ALIGN="center" class="invoice-gtgt-box-large">
                        <!-- Header - Hiển thị trên tất cả các trang -->
                        <tr class="invoice-gtgt-border-bottom">
                            <td width="24%" align="center">
                                <div class="image-box">
                                    <!-- Logo sẽ được thêm ở đây -->
                                </div>
                            </td>
                            <td width="48%" align="center">
                                <font class="invoice-gtgt-label-bold" style="font-size: 18px;">
                                    HÓA ĐƠN GIÁ TRỊ GIA TĂNG
                                </font>
                                <br>
                                <font class="invoice-gtgt-label-bold" style="font-size: 16px;">
                                    (VAT INVOICE)
                                </font>
                                <br>
                                <font class="invoice-gtgt-label-bold">Bản thể hiện của hóa đơn điện tử</font>
                                <br>
                                <font class="invoice-gtgt-label-italic">
                                    (Electronic invoice display)
                                </font>
                                <br>
                                <font class="invoice-gtgt-label-normal">Ngày </font>
                                <font class="invoice-gtgt-label-italic">(date) </font>
                                <span>${invoiceDataGtgt.invoiceDate}</span>
                            </td>
                            <td width="28%" class="invoice-gtgt-vertical-top">
                                <table align="right" class="invoice-gtgt-box-small invoice-gtgt-no-border">
                                    <tr>
                                        <td align="left">
                                            <font class="invoice-gtgt-label-bold">Ký hiệu</font>
                                            <font class="invoice-gtgt-label-italic">(Serial): </font>
                                        </td>
                                        <td align="left" class="invoice-gtgt-item-bold">
                                            ${invoiceDataGtgt.serialNo || '.........'}
                                        </td>
                                    </tr>
                                    <tr>
                                        <td align="left">
                                            <font class="invoice-gtgt-label-bold">Số</font>
                                            <font class="invoice-gtgt-label-italic">(No.): </font>
                                        </td>
                                        <td align="left" class="invoice-gtgt-item-bold">
                                            ${invoiceDataGtgt.invoiceNo || '.........'}
                                        </td>
                                    </tr>
                                </table>
                            </td>
                        </tr>

                        <tr class="invoice-gtgt-border-bottom">
                            <td colspan="3">
                                <table class="invoice-gtgt-width-100">
                                    <tr>
                                        <td>
                                            <font class="invoice-gtgt-label-bold">Đơn vị bán hàng</font>
                                            <font class="invoice-gtgt-label-italic">(Issued): </font>
                                            <font class="invoice-gtgt-label-bold">${invoiceDataGtgt.sellerName || '.........................................'}</font>
                                        </td>
                                    </tr>
                                    <tr>
                                        <td>
                                            <font class="invoice-gtgt-label-bold">Mã số thuế</font>
                                            <font class="invoice-gtgt-label-italic">(Tax code): </font>
                                            <font class="invoice-gtgt-label-bold">${invoiceDataGtgt.sellerTaxCode || '.........................................'}</font>
                                        </td>
                                    </tr>
                                    <tr>
                                        <td>
                                            <font class="invoice-gtgt-label-bold">Địa chỉ</font>
                                            <font class="invoice-gtgt-label-italic">(Address): </font>
                                            <font class="invoice-gtgt-label-bold">${invoiceDataGtgt.sellerAddress || '.........................................'}</font>
                                        </td>
                                    </tr>
                                </table>
                            </td>
                        </tr>

                        <tr>
                            <td colspan="3">
                                <table class="invoice-gtgt-width-100">
                                    <tr>
                                        <td>
                                            <font class="invoice-gtgt-label-bold">Họ tên người mua hàng</font>
                                            <font class="invoice-gtgt-label-italic">(Buyer name): </font>
                                            <font class="invoice-gtgt-item-normal">${invoiceDataGtgt.buyerName || '.........................................'}</font>
                                        </td>
                                    </tr>
                                    <tr>
                                        <td>
                                            <font class="invoice-gtgt-label-bold">Tên đơn vị</font>
                                            <font class="invoice-gtgt-label-italic">(Company name): </font>
                                            <font class="invoice-gtgt-item-normal">${invoiceDataGtgt.buyerName || '.........................................'}</font>
                                        </td>
                                    </tr>
                                    <tr>
                                        <td>
                                            <font class="invoice-gtgt-label-bold">Mã số thuế</font>
                                            <font class="invoice-gtgt-label-italic">(Tax code): </font>
                                            <font class="invoice-gtgt-item-bold">${invoiceDataGtgt.buyerTaxCode || '.........................................'}</font>
                                        </td>
                                    </tr>
                                    <tr>
                                        <td>
                                            <font class="invoice-gtgt-label-bold">Địa chỉ</font>
                                            <font class="invoice-gtgt-label-italic">(Address): </font>
                                            <font class="invoice-gtgt-item-normal">${invoiceDataGtgt.buyerAddress || '.........................................'}</font>
                                        </td>
                                    </tr>
                                </table>
                            </td>
                        </tr>

                        <tr>
                            <td colspan="3">
                                <table class="invoice-gtgt-width-100 invoice-gtgt-box-small">
                                    <tr>
                                        <td width="5%" align="center">
                                            <font class="invoice-gtgt-label-bold">STT</font>
                                            <br>
                                            <font class="invoice-gtgt-label-italic">(No.)</font>
                                        </td>
                                        <td width="35%" align="center">
                                            <font class="invoice-gtgt-label-bold">Tên hàng hóa, dịch vụ</font>
                                            <br>
                                            <font class="invoice-gtgt-label-italic">(Description)</font>
                                        </td>
                                        <td width="7%" align="center">
                                            <font class="invoice-gtgt-label-bold">ĐVT</font>
                                            <br>
                                            <font class="invoice-gtgt-label-italic">(Unit)</font>
                                        </td>
                                        <td width="7%" align="center">
                                            <font class="invoice-gtgt-label-bold">Số lượng</font>
                                            <br>
                                            <font class="invoice-gtgt-label-italic">(Quantity)</font>
                                        </td>
                                        <td width="10%" align="center">
                                            <font class="invoice-gtgt-label-bold">Đơn giá</font>
                                            <br>
                                            <font class="invoice-gtgt-label-italic">(Unit price)</font>
                                        </td>
                                        <td width="12%" align="center">
                                            <font class="invoice-gtgt-label-bold">Thành tiền</font>
                                            <br>
                                            <font class="invoice-gtgt-label-italic">(Amount)</font>
                                        </td>
                                    </tr>
                                    ${productsHtml}
                                    ${isLastPage ? `
                                        <!-- Tổng tiền hàng -->
                                        <tr>
                                            <td class="text-right box-small" colspan="5">
                                                <span class="label-bold">Cộng tiền hàng</span>
                                                <span class="label-italic">(Total amount):</span>
                                            </td>
                                            <td class="text-right box-small item-normal">${formatNumberGtgt(invoiceDataGtgt.totalAmount)}</td>
                                        </tr>

                                        <!-- Thuế GTGT -->
                                        <tr>
                                            <td class="text-left box-small label-normal" colspan="2">
                                                <span class="label-bold">Thuế suất GTGT</span>
                                                <span class="label-italic">(VAT rate):</span>
                                                <span class="label-normal">10%</span>
                                            </td>
                                            <td class="text-right box-small label-normal" colspan="3">
                                                <span class="label-bold">Tiền thuế GTGT </span>
                                                <span class="label-italic">(VAT amount):</span>
                                            </td>
                                            <td class="text-right box-small item-normal">${formatNumberGtgt(invoiceDataGtgt.vatAmount)}</td>
                                        </tr>

                                        <!-- Tổng thanh toán -->
                                        <tr>
                                            <td class="text-right box-small" colspan="5">
                                                <span class="label-bold">Tổng cộng tiền thanh toán</span>
                                                <span class="label-italic">(Total payment):</span>
                                            </td>
                                            <td class="text-right box-small item-normal">${formatNumberGtgt(invoiceDataGtgt.totalPayment)}</td>
                                        </tr>

                                        <!-- Số tiền viết bằng chữ -->
                                        <tr>
                                            <td class="text-left box-small" colspan="6">
                                                <span class="label-bold">Số tiền viết bằng chữ</span>
                                                <span class="label-italic">(Amount in words):</span>
                                                <span class="item-normal">${invoiceDataGtgt.amountInWords || '.........................................'}</span>
                                            </td>
                                        </tr>
                                    ` : ''}
                                </table>
                            </td>
                        </tr>

                        <!-- CHỈ HIỂN THỊ CHỮ KÝ Ở TRANG CUỐI -->
                        ${isLastPage ? `
                        <tr>
                            <td colspan="3">
                                <table class="invoice-gtgt-width-100">
                                    <tr>
                                        <td align="center" class="invoice-gtgt-width-100">
                                            <font class="invoice-gtgt-label-bold">Người mua hàng</font>
                                            <font class="invoice-gtgt-label-italic">(Buyer)</font>
                                        </td>
                                        <td align="center" class="invoice-gtgt-width-100">
                                            <font class="invoice-gtgt-label-bold">Người bán hàng</font>
                                            <font class="invoice-gtgt-label-italic">(Seller)</font>
                                        </td>
                                    </tr>
                                    <tr>
                                        <td align="center">
                                            <div style="height: 80px; border-bottom: 1px solid #000; width: 200px; margin: 0 auto;">
                                                <!-- Để trống phần chữ ký của bên mua -->
                                            </div>
                                        </td>
                                        <td align="center">
                                            <div style="height: 80px; border-bottom: 1px solid #ff0000; width: 200px; margin: 0 auto;">
                                                <font class="invoice-gtgt-label-bold" style="color: #ff0000;">
                                                    ✅ Ký bởi ${invoiceDataGtgt.sellerName || '.........................................'}
                                                </font>
                                                <br>
                                                <font class="invoice-gtgt-label-italic" style="color: #ff0000;">
                                                    (Chữ ký số)
                                                </font>
                                            </div>
                                        </td>
                                    </tr>
                                </table>
                            </td>
                        </tr>
                        ` : ''}
                        
                        <!-- Thông tin mã tra cứu và mã cơ quan thuế - HIỆN TRÊN TẤT CẢ CÁC TRANG -->
                        <tr>
                            <td colspan="3">
                                
                            </td>
                        </tr>
                    </table>
                    
                    <!-- Footer với mã CQT và mã tra cứu - HIỆN TRÊN TẤT CẢ CÁC TRANG -->
                    <div class="footer">
                        <div class="label-italic">(Cần kiểm tra đối chiếu khi lập, giao, nhận hóa đơn)</div>
                        
                        <!-- Mã CQT và mã tra cứu -->
                        <div class="cqt-info">
                            <div class="label-italic">Mã cơ quan thuế: <span class="cqt-code">${invoiceDataGtgt.taxAuthorityCode || '.........................................'}</span></div>
                            <div class="label-italic">Mã tra cứu: <span class="cqt-code">${verificationCode || '.........................................'}</span></div>
                        </div>
                        
                        <div class="label-italic">Đơn vị cung cấp dịch vụ Hóa đơn điện tử: ${serviceProviderName}, MST: ${serviceProviderTaxCode}</div>
                        <div class="label-italic">
                            Tra cứu hóa đơn điện tử tại Website: ${getServiceProviderUrl(serviceProviderTaxCode)}
                        </div>
                        <div class="label-italic" style="margin-top: 5px;">
                            Hoặc tra cứu tại Cổng thông tin điện tử của Tổng cục Thuế: https://tracuuhoadon.gdt.gov.vn
                        </div>
                    </div>
                    
                    <!-- Số trang - HIỆN TRÊN TẤT CẢ CÁC TRANG -->
                    <div class="page-number">Trang ${pageNumber}/${totalPages}</div>
                </div>
            `;
        }

        // Hàm lấy tên nhà cung cấp dịch vụ từ MSTTCGP
        function getServiceProviderName(taxCode) {
            if (!taxCode) return '.........................................';
            const provider = serviceProviders.find(p => p.mst === taxCode);
            return provider ? provider.name : '.........................................';
        }

        // Hàm lấy URL nhà cung cấp dịch vụ từ MSTTCGP
        function getServiceProviderUrl(taxCode) {
            if (!taxCode) return '.........................................';
            const provider = serviceProviders.find(p => p.mst === taxCode);
            return provider ? provider.url : '.........................................';
        }

        // Tạo preview hóa đơn đầy đủ cho modal
        function generateFullGtgtInvoice() {
            let fullInvoiceHtml = '';
            
            for (let i = 0; i < paginatedInvoiceDataGtgt.length; i++) {
                const pageData = paginatedInvoiceDataGtgt[i];
                fullInvoiceHtml += generateGtgtInvoicePage(pageData);
            }
            
            return fullInvoiceHtml;
        }

        // Xử lý sự kiện phân trang cho tab GTGT
        document.getElementById('prevPageBtn').addEventListener('click', function() {
            if (currentPageGtgt > 1) {
                showPageGtgt(currentPageGtgt - 1);
            }
        });

        document.getElementById('nextPageBtn').addEventListener('click', function() {
            if (currentPageGtgt < totalPagesGtgt) {
                showPageGtgt(currentPageGtgt + 1);
            }
        });

        // Hiển thị modal hóa đơn cho tab GTGT
        document.getElementById('showModalBtnGtgt').addEventListener('click', function() {
            const modalContent = document.getElementById('modalInvoiceContentGtgt');
            const invoiceHtml = generateFullGtgtInvoice();
            modalContent.innerHTML = invoiceHtml;
            document.getElementById('invoiceModalGtgt').style.display = 'block';
        });

        // Đóng modal hóa đơn cho tab GTGT
        document.getElementById('closeModalBtnGtgt').addEventListener('click', closeInvoiceModalGtgt);
        document.getElementById('closeModalBtn2Gtgt').addEventListener('click', closeInvoiceModalGtgt);
        
        function closeInvoiceModalGtgt() {
            document.getElementById('invoiceModalGtgt').style.display = 'none';
        }

        // In hóa đơn cho tab GTGT
        document.getElementById('printInvoiceBtnGtgt').addEventListener('click', function() {
            window.print();
        });

        // Tải PDF từ modal cho tab GTGT
        document.getElementById('downloadModalPdfBtnGtgt').addEventListener('click', function() {
            generatePDFWithScreenshotGtgt();
        });

        // Tải PDF từ preview cho tab GTGT
        document.getElementById('downloadPdfGtgt').addEventListener('click', function() {
            generatePDFWithScreenshotGtgt();
        });

        // YÊU CẦU 7: Xử lý yêu cầu hóa đơn gốc
        document.getElementById('requestOriginalBtn').addEventListener('click', function() {
            // Kiểm tra xem có dữ liệu từ XML không
            const jsonData = loadJsonFromStorage();
            if (!jsonData || !jsonData.processedData) {
                alert('Không có dữ liệu hóa đơn. Vui lòng tải lên file XML trước.');
                return;
            }
            
            // Cập nhật dữ liệu toàn cục từ JSON - YÊU CẦU 7 & 8
            invoiceData = jsonData.processedData;
            matchedProviders = findMatchingProviders(invoiceData.serviceProviderTaxCode);
            
            // Mở modal tra cứu với thông tin đã được đồng bộ
            document.getElementById('openLookupModalBtn').click();
            
            alert('Đã đồng bộ MSTTCGP từ hóa đơn GTGT. Vui lòng chọn nhà cung cấp trong modal tra cứu.');
        });

        // Hàm tải PDF sử dụng phương pháp chụp ảnh từng trang cho tab GTGT - CẢI THIỆN
        async function generatePDFWithScreenshotGtgt() {
            // Hiển thị trạng thái xử lý
            const processingStatus = document.getElementById('pdfProcessingStatus');
            const progressBar = document.getElementById('pdfProgressBar');
            const statusMessage = document.getElementById('pdfStatusMessage');
            
            processingStatus.style.display = 'block';
            progressBar.style.width = '0%';
            progressBar.textContent = '0%';
            statusMessage.textContent = 'Đang chuẩn bị tạo PDF...';

            try {
                const { jsPDF } = window.jspdf;
                const pdf = new jsPDF({
                    orientation: 'portrait',
                    unit: 'mm',
                    format: 'a4'
                });

                // Thiết lập metadata
                pdf.setProperties({
                    title: `Hóa đơn GTGT ${invoiceDataGtgt.invoiceNo}`,
                    subject: 'Hóa đơn giá trị gia tăng',
                    author: 'Công cụ chuyển đổi XML to PDF'
                });

                // Tạo từng trang và chụp ảnh riêng biệt
                for (let i = 0; i < paginatedInvoiceDataGtgt.length; i++) {
                    const pageData = paginatedInvoiceDataGtgt[i];
                    
                    // Cập nhật tiến trình
                    const progress = Math.round(((i + 1) / paginatedInvoiceDataGtgt.length) * 100);
                    progressBar.style.width = `${progress}%`;
                    progressBar.textContent = `${progress}%`;
                    statusMessage.textContent = `Đang xử lý trang ${i + 1}/${paginatedInvoiceDataGtgt.length}...`;

                    // Tạo nội dung trang trong container ẩn
                    const captureContainer = document.getElementById('captureContainer');
                    const invoiceHtml = generateGtgtInvoicePage(pageData);
                    captureContainer.innerHTML = invoiceHtml;

                    // Đảm bảo font Times New Roman được áp dụng
                    captureContainer.classList.add('force-times-new-roman');
                    captureContainer.querySelectorAll('*').forEach(el => {
                        el.classList.add('force-times-new-roman');
                    });

                    // Chờ một chút để đảm bảo DOM đã render
                    await new Promise(resolve => setTimeout(resolve, 500));

                    // Chụp ảnh container với scale phù hợp
                    const canvas = await html2canvas(captureContainer, {
                        scale: 1.5, // Scale phù hợp để đảm bảo chất lượng
                        useCORS: true,
                        allowTaint: true,
                        backgroundColor: '#ffffff',
                        logging: false,
                        width: captureContainer.scrollWidth,
                        height: captureContainer.scrollHeight,
                        onclone: function(clonedDoc) {
                            // Đảm bảo font được áp dụng trong clone
                            clonedDoc.querySelectorAll('*').forEach(el => {
                                el.style.fontFamily = 'Times New Roman, Times, serif !important';
                            });
                        }
                    });

                    // Tính toán kích thước để fit vào trang A4
                    const imgWidth = 210; // A4 width in mm
                    const imgHeight = (canvas.height * imgWidth) / canvas.width;

                    // Thêm ảnh vào PDF (thêm trang mới nếu không phải trang đầu)
                    if (i > 0) {
                        pdf.addPage();
                    }

                    // Thêm ảnh vào PDF
                    const imgData = canvas.toDataURL('image/jpeg', 0.9); // Chất lượng 90%
                    pdf.addImage(imgData, 'JPEG', 0, 0, imgWidth, imgHeight);
                }

                // Cập nhật tiến trình cuối cùng
                progressBar.style.width = '100%';
                progressBar.textContent = '100%';
                statusMessage.textContent = 'Đang lưu file...';

                // Lưu PDF
                const fileName = `HOA_DON_GTGT_${invoiceDataGtgt.invoiceNo || 'UNKNOWN'}.pdf`;
                pdf.save(fileName);

                // Ẩn trạng thái xử lý
                processingStatus.style.display = 'none';

                // Hiển thị thông báo thành công
                alert(`✅ PDF đã được tạo thành công với ${paginatedInvoiceDataGtgt.length} trang!`);

            } catch (error) {
                console.error('Lỗi khi tạo PDF:', error);
                processingStatus.style.display = 'none';
                alert('❌ Có lỗi xảy ra khi tạo PDF. Vui lòng thử lại.');
            }
        }

        // ==============================================
        // HÀM CẬP NHẬT FOOTER - ĐÃ ĐƯỢC THÊM
        // ==============================================

        // Hàm cập nhật link tra cứu ở footer
        function updateFooterLookupLink(link, providerName) {
            const footerLookupLink = document.getElementById('footerLookupLink');
            currentLookupLink = link;
            
            footerLookupLink.innerHTML = `
                <strong>🔗 Link tra cứu từ ${providerName}:</strong><br>
                <a href="${link}" target="_blank" style="color: #ffffff; text-decoration: underline; word-break: break-all;">
                    ${link}
                </a>
                <br>
                <small style="opacity: 0.8;">(Nhấn vào link để mở trong tab mới)</small>
            `;
            
            // Thêm hiệu ứng để thu hút sự chú ý
            const footerLookupInfo = document.getElementById('footerLookupInfo');
            footerLookupInfo.style.animation = 'pulse 1s ease-in-out';
            setTimeout(() => {
                footerLookupInfo.style.animation = '';
            }, 1000);
            
            console.log(`✅ Đã cập nhật link tra cứu ở footer: ${link}`);
        }

        // ==============================================
        // KHỞI TẠO VÀ CÁC HÀM CHUNG
        // ==============================================

        // Xử lý tab chính
        document.querySelectorAll('.tab').forEach(tab => {
            tab.addEventListener('click', function() {
                // Xóa active class từ tất cả các tab và nội dung
                document.querySelectorAll('.tab').forEach(t => t.classList.remove('active'));
                document.querySelectorAll('.tab-content').forEach(c => c.classList.remove('active'));
                
                // Thêm active class cho tab được chọn
                this.classList.add('active');
                
                // Hiển thị nội dung tương ứng
                const tabId = this.getAttribute('data-tab');
                document.getElementById(tabId).classList.add('active');
            });
        });

        // Hiển thị danh sách MSTTCGP ở footer
        function displayMSTTCGPList() {
            const msttcgpList = document.getElementById('msttcgpList');
            
            // Tạo danh sách các module chính
            const mainModules = [
                { mst: VIENH_HY_MODULE.mst, name: VIENH_HY_MODULE.name },
                { mst: MISA_MODULE.mst, name: MISA_MODULE.name },
                { mst: BKAV_MODULE.id, name: BKAV_MODULE.name },
                { mst: EASYINVOICE_MODULE.mst, name: EASYINVOICE_MODULE.name }
            ];
            
            // Thêm các module chính
            mainModules.forEach(module => {
                const item = document.createElement('div');
                item.className = 'msttcgp-item msttcgp-module';
                item.textContent = `${module.mst} - ${module.name}`;
                msttcgpList.appendChild(item);
            });
            
            // Thêm một số nhà cung cấp khác
            const otherProviders = serviceProviders.slice(0, 10); // Lấy 10 nhà cung cấp đầu tiên
            otherProviders.forEach(provider => {
                // Bỏ qua các module đã thêm
                if (!mainModules.some(module => module.mst === provider.mst)) {
                    const item = document.createElement('div');
                    item.className = 'msttcgp-item';
                    item.textContent = `${provider.mst} - ${provider.name}`;
                    msttcgpList.appendChild(item);
                }
            });
        }

        // Khởi tạo khi trang được tải
        document.addEventListener('DOMContentLoaded', function() {
            // Khởi tạo danh sách nhà cung cấp
            initializeProviders();
            
            // Hiển thị danh sách MSTTCGP
            displayMSTTCGPList();
            
            // Kiểm tra xem có dữ liệu JSON trong localStorage không - YÊU CẦU 1
            const jsonData = loadJsonFromStorage();
            if (jsonData) {
                console.log('Đã tìm thấy dữ liệu JSON trong localStorage, đang khôi phục...');
                
                // Khôi phục dữ liệu từ JSON - YÊU CẦU 1 & 8
                invoiceData = jsonData.processedData || {};
                
                // Tìm nhà cung cấp phù hợp dựa trên MSTTCGP - YÊU CẦU 2
                matchedProviders = findMatchingProviders(invoiceData.serviceProviderTaxCode);
                
                console.log('Đã khôi phục dữ liệu từ JSON thành công');
            }
            
            console.log('Công cụ chuyển đổi hóa đơn điện tử đã được khởi tạo');
            console.log('Module Vĩnh Hy:', VIENH_HY_MODULE);
            console.log('Module MISA:', MISA_MODULE);
            console.log('Module BKAV:', BKAV_MODULE);
            console.log('Module EasyInvoice:', EASYINVOICE_MODULE);
        });
    </script>
</body>
</html>
