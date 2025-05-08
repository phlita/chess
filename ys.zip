<?php
// 如果是 POST 请求，处理数据生成逻辑
if ($_SERVER['REQUEST_METHOD'] === 'POST') {
    header('Content-Type: application/json');
    date_default_timezone_set('Asia/Shanghai');

    // 连接到 SQLite3 数据库并创建表
    $db = new SQLite3('monitoring.db');
    $db->exec('CREATE TABLE IF NOT EXISTS monitoring (
        id INTEGER PRIMARY KEY AUTOINCREMENT,
        website TEXT,
        timestamp DATETIME DEFAULT CURRENT_TIMESTAMP,
        ping FLOAT, 
        online_php_status TEXT,
        online_php_response_time FLOAT,
        online_php_http_code INTEGER
    )');

    // 函数：生成时间段内的多个时间戳
    function generateTimestamps($period, $frequency) {
        $timestamps = [];
        $currentTime = time();

        // 根据用户选择的周期确定开始时间
        switch ($period) {
            case 'day':
                $startTime = strtotime('-1 day', $currentTime);
                break;
            case 'week':
                $startTime = strtotime('-1 week', $currentTime);
                break;
            case 'month':
                $startTime = strtotime('-1 month', $currentTime);
                break;
            case 'quarter':
                $startTime = strtotime('-3 months', $currentTime);
                break;
            case 'half_year':
                $startTime = strtotime('-6 months', $currentTime);
                break;
            case 'year':
                $startTime = strtotime('-1 year', $currentTime);
                break;
            default:
                $startTime = $currentTime;
        }

        // 生成时间戳，频率决定每天监控的次数
        $interval = ($currentTime - $startTime) / $frequency;
        for ($i = 0; $i < $frequency; $i++) {
            $timestamps[] = date('Y-m-d H:i:s', $startTime + ($i * $interval));
        }

        return $timestamps;
    }

    // 函数：生成演示数据
    function generateDemoData($websiteCount, $period, $frequency) {
        $data = [];
        for ($i = 0; $i < $websiteCount; $i++) {
            $url = 'http://example' . ($i + 1) . '.com';
            $timestamps = generateTimestamps($period, $frequency);

            foreach ($timestamps as $timestamp) {
                $ping = rand(20, 300);  // 随机生成 20 到 300 毫秒的响应时间
                $onlinePhpStatus = rand(0, 1) ? '200' : '404';  // 随机返回 200 或 404 状态
                $onlinePhpResponseTime = rand(50, 500);  // 随机生成 50 到 500 毫秒的响应时间
                $onlinePhpHttpCode = $onlinePhpStatus === '200' ? 200 : 404;

                $data[] = [
                    'website' => $url,
                    'ping' => $ping,
                    'onlinePhpStatus' => $onlinePhpStatus,
                    'onlinePhpResponseTime' => $onlinePhpResponseTime,
                    'onlinePhpHttpCode' => $onlinePhpHttpCode,
                    'timestamp' => $timestamp
                ];
            }
        }
        return $data;
    }

    // 获取用户输入参数
    $websiteCount = isset($_POST['websiteCount']) ? (int)$_POST['websiteCount'] : 3;  // 默认3个网站
    $period = isset($_POST['period']) ? $_POST['period'] : 'day';  // 默认最近一天
    $frequency = isset($_POST['frequency']) ? (int)$_POST['frequency'] : 24;  // 默认一天24次

    // 生成演示数据
    $demoData = generateDemoData($websiteCount, $period, $frequency);

    // 将生成的数据写入 SQLite3 数据库
    $stmt = $db->prepare('INSERT INTO monitoring (website, ping, online_php_status, online_php_response_time, online_php_http_code, timestamp) VALUES (:website, :ping, :online_php_status, :online_php_response_time, :online_php_http_code, :timestamp)');
 
  foreach ($demoData as $data) {
        $stmt->bindValue(':website', $data['website'], SQLITE3_TEXT);
        $stmt->bindValue(':ping', $data['ping'], SQLITE3_FLOAT);
        $stmt->bindValue(':online_php_status', $data['onlinePhpStatus'], SQLITE3_TEXT);
        $stmt->bindValue(':online_php_response_time', $data['onlinePhpResponseTime'], SQLITE3_FLOAT);
        $stmt->bindValue(':online_php_http_code', $data['onlinePhpHttpCode'], SQLITE3_INTEGER);
        $stmt->bindValue(':timestamp', $data['timestamp'], SQLITE3_TEXT);
        $stmt->execute();
    }

 echo "成功";  


}
?>

<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>生成监控演示数据</title>
</head>
<body>
    <h1>生成监控演示数据</h1>
    <form id="demoForm" method="post">
        <label for="websiteCount">输入要生成的网站数量:</label>
        <input type="number" id="websiteCount" name="websiteCount" min="1" value="3" required>
        <br><br>

        <label for="period">选择时间周期:</label>
        <select id="period" name="period">
            <option value="day">最近一天</option>
            <option value="week">最近一周</option>
            <option value="month">最近一个月</option>
            <option value="quarter">最近一季度</option>
            <option value="half_year">最近半年</option>
            <option value="year">最近一年</option>
        </select>
        <br><br>

        <label for="frequency">设置每天的监控频率:</label>
        <input type="number" id="frequency" name="frequency" min="1" value="24" required>
        <br><br>

        <button type="submit">生成数据</button>
    </form>
    
    <div id="result"></div>

    <script>
        document.getElementById('demoForm').addEventListener('submit', function(event) {
            event.preventDefault();  // 防止表单默认提交

            const formData = new FormData(this);
            fetch('', {
                method: 'POST',
                body: formData
            })
            .then(response => response.json())
            .then(data => {
                let resultDiv = document.getElementById('result');
                if (data.results) {
                    resultDiv.innerHTML = '<h2>生成结果:</h2><pre>' + JSON.stringify(data.results, null, 2) + '</pre>';
                } else {
                    resultDiv.innerHTML = '<h2>生成错误:</h2><pre>' + JSON.stringify(data, null, 2) + '</pre>';
                }
            })
            .catch(error => {
                console.error('Error:', error);
            });
        });
    </script>
</body>
</html>
