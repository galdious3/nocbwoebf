<?php
/**
 * نظام حجز الطيران - ملف PHP موحد
 * =================================
 * هذا الملف يحتوي على جميع الأكواد اللازمة لتشغيل موقع حجز الطيران
 * بما في ذلك:
 * - دوال معالجة قاعدة البيانات
 * - نظام تسجيل الدخول والتسجيل
 * - البحث عن الرحلات وعرضها
 * - حجز الرحلات
 * - تسجيل الصعود للرحلة
 * - إصدار بطاقات الصعود
 * - واجهات المستخدم وتنسيقاتها
 */

// ===================================================
// إعدادات الموقع الأساسية
// ===================================================
session_start();

// إعدادات اتصال قاعدة البيانات
$db_host = 'localhost';
$db_user = 'root';
$db_password = '';
$db_name = 'mustafakk';

// متغيرات الصفحة
$page = isset($_GET['page']) ? $_GET['page'] : 'home';
$public_pages = ['home', 'login', 'register', 'search_flights', 'flight_details', 'book_flight', 'payment', 'booking_confirmation', 'booking_lookup', 'check_in', 'boarding_pass'];
$protected_pages = ['profile', 'my_bookings'];

// إنشاء اتصال بقاعدة البيانات
$conn = new mysqli($db_host, $db_user, $db_password, $db_name);

// التحقق من وجود أخطاء في الاتصال
if ($conn->connect_error) {
    die("فشل الاتصال بقاعدة البيانات: " . $conn->connect_error);
}

// تعيين ترميز الاتصال إلى UTF-8
$conn->set_charset("utf8mb4");

// ===================================================
// دوال معالجة قاعدة البيانات
// ===================================================

/**
 * استعلام يبحث المدن المتاحة ونقاط المغادرة
 * 
 * @param mysqli $conn اتصال قاعدة البيانات
 * @param string $query نص البحث
 * @return array مصفوفة تحتوي على نتائج البحث
 */
function searchCities($conn, $query = '') {
    $results = [];
    
    // بناء الاستعلام
    $sql = "SELECT id, name, country, airport_name, airport_code 
            FROM cities 
            WHERE popular_destination = 1";
    
    // إضافة شرط البحث إذا تم تحديده
    if (!empty($query)) {
        $query = '%' . $conn->real_escape_string($query) . '%';
        $sql = "SELECT id, name, country, airport_name, airport_code 
                FROM cities 
                WHERE name LIKE ? OR country LIKE ? OR airport_name LIKE ? OR airport_code LIKE ?
                ORDER BY popular_destination DESC, name ASC";
        
        $stmt = $conn->prepare($sql);
        $stmt->bind_param('ssss', $query, $query, $query, $query);
    } else {
        $stmt = $conn->prepare($sql);
    }
    
    // تنفيذ الاستعلام
    $stmt->execute();
    $result = $stmt->get_result();
    
    // معالجة النتائج
    if ($result->num_rows > 0) {
        while ($row = $result->fetch_assoc()) {
            $results[] = [
                'id' => $row['id'],
                'name' => $row['name'],
                'country' => $row['country'],
                'airport_name' => $row['airport_name'],
                'airport_code' => $row['airport_code'],
                'display_name' => $row['name'] . ' (' . $row['airport_code'] . '), ' . $row['country']
            ];
        }
    }
    
    return $results;
}

/**
 * البحث عن رحلات طيران بناءً على معايير محددة
 * 
 * @param mysqli $conn اتصال قاعدة البيانات
 * @param int $from_city_id معرف مدينة المغادرة
 * @param int $to_city_id معرف مدينة الوصول
 * @param string $departure_date تاريخ المغادرة
 * @param string $return_date تاريخ العودة (اختياري)
 * @param int $passengers عدد المسافرين
 * @param string $class درجة السفر (Economy، Business، First)
 * @return array مصفوفة تحتوي على نتائج البحث
 */
function searchFlights($conn, $from_city_id, $to_city_id, $departure_date, $return_date = null, $passengers = 1, $class = 'Economy') {
    $results = [
        'outbound' => [],
        'return' => []
    ];
    
    // تحويل التاريخ إلى رقم اليوم في الأسبوع (0=الأحد، 1=الاثنين، ..، 6=السبت)
    $day_of_week = date('w', strtotime($departure_date));
    
    // البحث عن رحلات الذهاب
    $sql = "SELECT fs.*, 
                   a.name as airline_name, a.logo_url, a.code as airline_code,
                   fc.name as from_city_name, fc.airport_code as from_airport_code,
                   tc.name as to_city_name, tc.airport_code as to_airport_code,
                   at.model as aircraft_model, at.total_seats,
                   fp.base_price, fp.season_type
            FROM flight_schedules fs
            JOIN airlines a ON fs.airline_id = a.id
            JOIN cities fc ON fs.from_city_id = fc.id
            JOIN cities tc ON fs.to_city_id = tc.id
            JOIN aircraft_types at ON fs.aircraft_type_id = at.id
            LEFT JOIN flight_prices fp ON fs.id = fp.flight_schedule_id AND fp.travel_class = ?
            WHERE fs.from_city_id = ? AND fs.to_city_id = ? AND fs.day_of_week = ? AND fs.status = 'Active'
            ORDER BY fp.base_price ASC";
            
    $stmt = $conn->prepare($sql);
    $stmt->bind_param('siis', $class, $from_city_id, $to_city_id, $day_of_week);
    $stmt->execute();
    $outbound_result = $stmt->get_result();
    
    // معالجة نتائج رحلات الذهاب
    if ($outbound_result->num_rows > 0) {
        while ($row = $outbound_result->fetch_assoc()) {
            // تعديل الأسعار بناءً على عدد المسافرين ودرجة السفر
            $base_price = $row['base_price'] ?: 100; // سعر افتراضي إذا لم يتم تحديد سعر
            
            // تطبيق مضاعف السعر حسب درجة السفر
            if ($class === 'Business') {
                $base_price *= 2.5;
            } elseif ($class === 'First') {
                $base_price *= 4;
            }
            
            // تطبيق مضاعف السعر حسب موسم السفر
            if ($row['season_type'] === 'Peak') {
                $base_price *= 1.3;
            } elseif ($row['season_type'] === 'Off-peak') {
                $base_price *= 0.8;
            }
            
            // تقريب السعر لأقرب رقم صحيح
            $base_price = round($base_price);
            
            // حساب السعر النهائي للمسافر الواحد (مع الضرائب والرسوم)
            $taxes = $base_price * 0.15;
            $total_price = $base_price + $taxes;
            
            // إجمالي السعر لجميع المسافرين
            $total_price_all_passengers = $total_price * $passengers;
            
            // إضافة النتيجة
            $results['outbound'][] = [
                'id' => $row['id'],
                'airline_name' => $row['airline_name'],
                'airline_code' => $row['airline_code'],
                'logo_url' => $row['logo_url'],
                'flight_number' => $row['flight_number'],
                'from_city_id' => $row['from_city_id'],
                'from_city_name' => $row['from_city_name'],
                'from_airport_code' => $row['from_airport_code'],
                'to_city_id' => $row['to_city_id'],
                'to_city_name' => $row['to_city_name'],
                'to_airport_code' => $row['to_airport_code'],
                'departure_time' => $row['departure_time'],
                'arrival_time' => $row['arrival_time'],
                'flight_duration' => $row['flight_duration'],
                'aircraft_model' => $row['aircraft_model'],
                'available_seats' => $row['total_seats'] - rand(5, 50), // عدد تقريبي للمقاعد المتاحة
                'base_price' => $base_price,
                'taxes' => $taxes,
                'total_price' => $total_price,
                'total_price_all_passengers' => $total_price_all_passengers,
                'currency' => 'USD',
                'departure_date' => $departure_date
            ];
        }
    }
    
    // إذا تم تحديد رحلة عودة، ابحث عن رحلات العودة
    if ($return_date) {
        $return_day_of_week = date('w', strtotime($return_date));
        
        $stmt = $conn->prepare($sql);
        $stmt->bind_param('siis', $class, $to_city_id, $from_city_id, $return_day_of_week);
        $stmt->execute();
        $return_result = $stmt->get_result();
        
        // معالجة نتائج رحلات العودة
        if ($return_result->num_rows > 0) {
            while ($row = $return_result->fetch_assoc()) {
                // نفس العمليات السابقة لحساب الأسعار
                $base_price = $row['base_price'] ?: 100; // سعر افتراضي إذا لم يتم تحديد سعر
                
                if ($class === 'Business') {
                    $base_price *= 2.5;
                } elseif ($class === 'First') {
                    $base_price *= 4;
                }
                
                if ($row['season_type'] === 'Peak') {
                    $base_price *= 1.3;
                } elseif ($row['season_type'] === 'Off-peak') {
                    $base_price *= 0.8;
                }
                
                $base_price = round($base_price);
                $taxes = $base_price * 0.15;
                $total_price = $base_price + $taxes;
                $total_price_all_passengers = $total_price * $passengers;
                
                $results['return'][] = [
                    'id' => $row['id'],
                    'airline_name' => $row['airline_name'],
                    'airline_code' => $row['airline_code'],
                    'logo_url' => $row['logo_url'],
                    'flight_number' => $row['flight_number'],
                    'from_city_id' => $row['from_city_id'],
                    'from_city_name' => $row['from_city_name'],
                    'from_airport_code' => $row['from_airport_code'],
                    'to_city_id' => $row['to_city_id'],
                    'to_city_name' => $row['to_city_name'],
                    'to_airport_code' => $row['to_airport_code'],
                    'departure_time' => $row['departure_time'],
                    'arrival_time' => $row['arrival_time'],
                    'flight_duration' => $row['flight_duration'],
                    'aircraft_model' => $row['aircraft_model'],
                    'available_seats' => $row['total_seats'] - rand(5, 50), // عدد تقريبي للمقاعد المتاحة
                    'base_price' => $base_price,
                    'taxes' => $taxes,
                    'total_price' => $total_price,
                    'total_price_all_passengers' => $total_price_all_passengers,
                    'currency' => 'USD',
                    'departure_date' => $return_date
                ];
            }
        }
    }
    
    return $results;
}

/**
 * الحصول على تفاصيل رحلة طيران محددة
 * 
 * @param mysqli $conn اتصال قاعدة البيانات
 * @param int $flight_id معرف الرحلة
 * @param string $class درجة السفر (اختياري)
 * @return array|null تفاصيل الرحلة أو null إذا لم يتم العثور على الرحلة
 */
function getFlightDetails($conn, $flight_id, $class = 'Economy') {
    $sql = "SELECT fs.*, 
                   a.name as airline_name, a.logo_url, a.code as airline_code, a.rating as airline_rating,
                   fc.name as from_city_name, fc.airport_code as from_airport_code, fc.country as from_country,
                   tc.name as to_city_name, tc.airport_code as to_airport_code, tc.country as to_country,
                   at.model as aircraft_model, at.has_wifi, at.has_entertainment, at.has_power_outlets,
                   fp.base_price, fp.season_type
            FROM flight_schedules fs
            JOIN airlines a ON fs.airline_id = a.id
            JOIN cities fc ON fs.from_city_id = fc.id
            JOIN cities tc ON fs.to_city_id = tc.id
            JOIN aircraft_types at ON fs.aircraft_type_id = at.id
            LEFT JOIN flight_prices fp ON fs.id = fp.flight_schedule_id AND fp.travel_class = ?
            WHERE fs.id = ?";
            
    $stmt = $conn->prepare($sql);
    $stmt->bind_param('si', $class, $flight_id);
    $stmt->execute();
    $result = $stmt->get_result();
    
    if ($result->num_rows > 0) {
        $row = $result->fetch_assoc();
        
        // تعديل الأسعار بناءً على درجة السفر
        $base_price = $row['base_price'] ?: 100; // سعر افتراضي إذا لم يتم تحديد سعر
        
        // تطبيق مضاعف السعر حسب درجة السفر
        if ($class === 'Business') {
            $base_price *= 2.5;
        } elseif ($class === 'First') {
            $base_price *= 4;
        }
        
        // تطبيق مضاعف السعر حسب موسم السفر
        if ($row['season_type'] === 'Peak') {
            $base_price *= 1.3;
        } elseif ($row['season_type'] === 'Off-peak') {
            $base_price *= 0.8;
        }
        
        // تقريب السعر لأقرب رقم صحيح
        $base_price = round($base_price);
        
        // حساب الضرائب والسعر النهائي
        $taxes = $base_price * 0.15;
        $total_price = $base_price + $taxes;
        
        // الحصول على معلومات إضافية عن الرحلة
        $fare_rules = getFareRules($conn, $row['airline_id'], $class);
        $ancillary_services = getAncillaryServices($conn, $row['airline_id']);
        
        return [
            'id' => $row['id'],
            'airline_id' => $row['airline_id'],
            'airline_name' => $row['airline_name'],
            'airline_code' => $row['airline_code'],
            'logo_url' => $row['logo_url'],
            'airline_rating' => $row['airline_rating'],
            'flight_number' => $row['flight_number'],
            'from_city_id' => $row['from_city_id'],
            'from_city_name' => $row['from_city_name'],
            'from_airport_code' => $row['from_airport_code'],
            'from_country' => $row['from_country'],
            'to_city_id' => $row['to_city_id'],
            'to_city_name' => $row['to_city_name'],
            'to_airport_code' => $row['to_airport_code'],
            'to_country' => $row['to_country'],
            'departure_terminal' => $row['departure_terminal'],
            'arrival_terminal' => $row['arrival_terminal'],
            'departure_time' => $row['departure_time'],
            'arrival_time' => $row['arrival_time'],
            'flight_duration' => $row['flight_duration'],
            'distance' => $row['distance'],
            'aircraft_model' => $row['aircraft_model'],
            'has_wifi' => $row['has_wifi'],
            'has_entertainment' => $row['has_entertainment'],
            'has_power_outlets' => $row['has_power_outlets'],
            'available_seats' => rand(5, 100), // عدد تقريبي للمقاعد المتاحة
            'base_price' => $base_price,
            'taxes' => $taxes,
            'total_price' => $total_price,
            'currency' => 'USD',
            'fare_rules' => $fare_rules,
            'ancillary_services' => $ancillary_services,
            'day_of_week' => $row['day_of_week']
        ];
    }
    
    return null;
}

/**
 * الحصول على قواعد التسعير لشركة طيران ودرجة سفر محددة
 * 
 * @param mysqli $conn اتصال قاعدة البيانات
 * @param int $airline_id معرف شركة الطيران
 * @param string $travel_class درجة السفر
 * @return array مصفوفة تحتوي على قواعد التسعير
 */
function getFareRules($conn, $airline_id, $travel_class) {
    $fare_rules = [];
    
    $sql = "SELECT * FROM fare_rules WHERE airline_id = ? AND travel_class = ?";
    $stmt = $conn->prepare($sql);
    $stmt->bind_param('is', $airline_id, $travel_class);
    $stmt->execute();
    $result = $stmt->get_result();
    
    if ($result->num_rows > 0) {
        while ($row = $result->fetch_assoc()) {
            $fare_rules[] = [
                'id' => $row['id'],
                'fare_type' => $row['fare_type'],
                'cancellation_fee' => $row['cancellation_fee'],
                'change_fee' => $row['change_fee'],
                'baggage_allowance' => $row['baggage_allowance'],
                'baggage_weight_limit' => $row['baggage_weight_limit'],
                'refundable' => $row['refundable'],
                'miles_accrual_rate' => $row['miles_accrual_rate'],
                'priority_boarding' => $row['priority_boarding'],
                'lounge_access' => $row['lounge_access']
            ];
        }
    } else {
        // إذا لم يتم العثور على قواعد، أضف قواعد افتراضية
        $default_baggage = ($travel_class === 'Economy') ? 1 : (($travel_class === 'Business') ? 2 : 3);
        $default_weight = ($travel_class === 'Economy') ? 23 : (($travel_class === 'Business') ? 32 : 40);
        $default_refundable = ($travel_class === 'Economy') ? 0 : 1;
        $default_miles = ($travel_class === 'Economy') ? 100 : (($travel_class === 'Business') ? 150 : 200);
        $default_priority = ($travel_class === 'Economy') ? 0 : 1;
        $default_lounge = ($travel_class === 'Economy') ? 0 : (($travel_class === 'Business') ? 1 : 1);
        
        $fare_rules[] = [
            'id' => 0,
            'fare_type' => 'Standard',
            'cancellation_fee' => ($travel_class === 'Economy') ? 100 : 50,
            'change_fee' => ($travel_class === 'Economy') ? 75 : 25,
            'baggage_allowance' => $default_baggage,
            'baggage_weight_limit' => $default_weight,
            'refundable' => $default_refundable,
            'miles_accrual_rate' => $default_miles,
            'priority_boarding' => $default_priority,
            'lounge_access' => $default_lounge
        ];
    }
    
    return $fare_rules;
}

/**
 * الحصول على الخدمات الإضافية لشركة طيران محددة
 * 
 * @param mysqli $conn اتصال قاعدة البيانات
 * @param int $airline_id معرف شركة الطيران
 * @return array مصفوفة تحتوي على الخدمات الإضافية
 */
function getAncillaryServices($conn, $airline_id) {
    $services = [];
    
    $sql = "SELECT * FROM ancillary_services WHERE airline_id = ?";
    $stmt = $conn->prepare($sql);
    $stmt->bind_param('i', $airline_id);
    $stmt->execute();
    $result = $stmt->get_result();
    
    if ($result->num_rows > 0) {
        while ($row = $result->fetch_assoc()) {
            $services[] = [
                'id' => $row['id'],
                'service_name' => $row['service_name'],
                'service_type' => $row['service_type'],
                'description' => $row['description'],
                'price' => $row['price'],
                'currency' => $row['currency']
            ];
        }
    } else {
        // إذا لم يتم العثور على خدمات، أضف خدمات افتراضية
        $services = [
            [
                'id' => 1,
                'service_name' => 'وجبة فاخرة',
                'service_type' => 'Meal',
                'description' => 'وجبة فاخرة بتشكيلة متنوعة من الأطباق العالمية',
                'price' => 25,
                'currency' => 'USD'
            ],
            [
                'id' => 2,
                'service_name' => 'مقعد مميز',
                'service_type' => 'Seat',
                'description' => 'مقعد بمساحة إضافية للقدمين',
                'price' => 35,
                'currency' => 'USD'
            ],
            [
                'id' => 3,
                'service_name' => 'حقيبة إضافية',
                'service_type' => 'Baggage',
                'description' => 'حقيبة إضافية بوزن 23 كجم',
                'price' => 50,
                'currency' => 'USD'
            ],
            [
                'id' => 4,
                'service_name' => 'تأمين السفر',
                'service_type' => 'Insurance',
                'description' => 'تغطية تأمينية شاملة للسفر تشمل المصاريف الطبية وتأخر الرحلات وفقدان الأمتعة',
                'price' => 20,
                'currency' => 'USD'
            ]
        ];
    }
    
    return $services;
}

/**
 * إنشاء رقم حجز عشوائي فريد
 * 
 * @param mysqli $conn اتصال قاعدة البيانات
 * @return string رقم الحجز العشوائي
 */
function generateBookingReference($conn) {
    $characters = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ';
    $digits = '0123456789';
    
    do {
        // إنشاء رمز من حرفين متبوعين بستة أرقام (مثل AB-123456)
        $reference = $characters[rand(0, strlen($characters) - 1)] . $characters[rand(0, strlen($characters) - 1)] . '-';
        
        for ($i = 0; $i < 6; $i++) {
            $reference .= $digits[rand(0, strlen($digits) - 1)];
        }
        
        // التحقق من عدم وجود رقم الحجز في قاعدة البيانات
        $sql = "SELECT COUNT(*) as count FROM bookings WHERE booking_reference = ?";
        $stmt = $conn->prepare($sql);
        $stmt->bind_param('s', $reference);
        $stmt->execute();
        $result = $stmt->get_result();
        $row = $result->fetch_assoc();
        
        // استمر في توليد أرقام حجز جديدة إذا كان الرقم موجودًا بالفعل
    } while ($row['count'] > 0);
    
    return $reference;
}

/**
 * إنشاء حجز جديد في قاعدة البيانات
 * 
 * @param mysqli $conn اتصال قاعدة البيانات
 * @param array $booking_data بيانات الحجز
 * @return string|bool رقم الحجز إذا تم الإنشاء بنجاح، أو false في حالة الفشل
 */
function createBooking($conn, $booking_data) {
    // إنشاء رقم حجز فريد
    $booking_reference = generateBookingReference($conn);
    
    // إعداد استعلام الإدخال
    $sql = "INSERT INTO bookings (
                booking_reference, passenger_name, passenger_email, passenger_phone,
                passenger_nationality, passenger_passport, passenger_dob,
                flight_schedule_id, travel_class, fare_type, seat_preference,
                bags_count, bags_weight, meal_preference, special_assistance,
                payment_method, payment_status, base_price, taxes_fees,
                ancillary_services_price, discount_amount, final_price, currency,
                booking_date, booking_status
            ) VALUES (
                ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, NOW(), 'Confirmed'
            )";
            
    $stmt = $conn->prepare($sql);
    $stmt->bind_param(
        'sssssssisssiiisssdddddss',
        $booking_reference,
        $booking_data['passenger_name'],
        $booking_data['passenger_email'],
        $booking_data['passenger_phone'],
        $booking_data['passenger_nationality'],
        $booking_data['passenger_passport'],
        $booking_data['passenger_dob'],
        $booking_data['flight_schedule_id'],
        $booking_data['travel_class'],
        $booking_data['fare_type'],
        $booking_data['seat_preference'],
        $booking_data['bags_count'],
        $booking_data['bags_weight'],
        $booking_data['meal_preference'],
        $booking_data['special_assistance'],
        $booking_data['payment_method'],
        $booking_data['payment_status'],
        $booking_data['base_price'],
        $booking_data['taxes_fees'],
        $booking_data['ancillary_services_price'],
        $booking_data['discount_amount'],
        $booking_data['final_price'],
        $booking_data['currency']
    );
    
    // تنفيذ الاستعلام
    if ($stmt->execute()) {
        // إضافة الخدمات الإضافية إذا وجدت
        if (!empty($booking_data['ancillary_services'])) {
            $booking_id = $conn->insert_id;
            
            foreach ($booking_data['ancillary_services'] as $service) {
                $sql = "INSERT INTO booking_ancillary_services (
                            booking_id, ancillary_service_id, quantity, price, currency
                        ) VALUES (?, ?, ?, ?, ?)";
                        
                $stmt = $conn->prepare($sql);
                $quantity = 1;
                $stmt->bind_param('iiids', $booking_id, $service['id'], $quantity, $service['price'], $service['currency']);
                $stmt->execute();
            }
        }
        
        // إنشاء معاملة دفع
        $sql = "INSERT INTO payment_transactions (
                    booking_id, transaction_reference, payment_method,
                    payment_amount, currency, payment_status, payment_date,
                    card_last_four
                ) VALUES (
                    ?, ?, ?, ?, ?, ?, NOW(), ?
                )";
                
        $booking_id = $conn->insert_id;
        $transaction_reference = 'TR-' . strtoupper(bin2hex(random_bytes(6)));
        $card_last_four = substr($booking_data['card_number'], -4);
        
        $stmt = $conn->prepare($sql);
        $stmt->bind_param(
            'issdsss',
            $booking_id,
            $transaction_reference,
            $booking_data['payment_method'],
            $booking_data['final_price'],
            $booking_data['currency'],
            $booking_data['payment_status'],
            $card_last_four
        );
        $stmt->execute();
        
        return $booking_reference;
    }
    
    return false;
}

/**
 * الحصول على تفاصيل الحجز بناءً على رقم الحجز
 * 
 * @param mysqli $conn اتصال قاعدة البيانات
 * @param string $booking_reference رقم الحجز
 * @return array|null تفاصيل الحجز أو null إذا لم يتم العثور على الحجز
 */
function getBookingDetails($conn, $booking_reference) {
    $sql = "SELECT b.*,
                   fs.flight_number, fs.departure_time, fs.arrival_time, fs.flight_duration,
                   a.name as airline_name, a.logo_url,
                   fc.name as from_city_name, fc.airport_code as from_airport_code,
                   tc.name as to_city_name, tc.airport_code as to_airport_code
            FROM bookings b
            JOIN flight_schedules fs ON b.flight_schedule_id = fs.id
            JOIN airlines a ON fs.airline_id = a.id
            JOIN cities fc ON fs.from_city_id = fc.id
            JOIN cities tc ON fs.to_city_id = tc.id
            WHERE b.booking_reference = ?";
            
    $stmt = $conn->prepare($sql);
    $stmt->bind_param('s', $booking_reference);
    $stmt->execute();
    $result = $stmt->get_result();
    
    if ($result->num_rows > 0) {
        return $result->fetch_assoc();
    }
    
    return null;
}

/**
 * الحصول على حجوزات المستخدم
 * 
 * @param mysqli $conn اتصال قاعدة البيانات
 * @param int $user_id معرف المستخدم
 * @return array مصفوفة تحتوي على حجوزات المستخدم
 */
function getUserBookings($conn, $user_id) {
    $bookings = [];
    
    $sql = "SELECT b.*,
                   fs.flight_number, fs.departure_time, fs.arrival_time,
                   a.name as airline_name, a.logo_url,
                   fc.name as from_city_name, fc.airport_code as from_airport_code,
                   tc.name as to_city_name, tc.airport_code as to_airport_code
            FROM bookings b
            JOIN flight_schedules fs ON b.flight_schedule_id = fs.id
            JOIN airlines a ON fs.airline_id = a.id
            JOIN cities fc ON fs.from_city_id = fc.id
            JOIN cities tc ON fs.to_city_id = tc.id
            WHERE b.user_id = ?
            ORDER BY b.booking_date DESC";
            
    $stmt = $conn->prepare($sql);
    $stmt->bind_param('i', $user_id);
    $stmt->execute();
    $result = $stmt->get_result();
    
    if ($result->num_rows > 0) {
        while ($row = $result->fetch_assoc()) {
            $bookings[] = $row;
        }
    }
    
    return $bookings;
}

/**
 * تنسيق مدة الرحلة من دقائق إلى ساعات ودقائق
 * 
 * @param int $minutes عدد الدقائق
 * @return string المدة المنسقة (مثل "2 ساعة 15 دقيقة")
 */
function formatFlightDuration($minutes) {
    $hours = floor($minutes / 60);
    $mins = $minutes % 60;
    
    $duration = '';
    
    if ($hours > 0) {
        $duration .= $hours . ' ساعة ';
    }
    
    if ($mins > 0) {
        $duration .= $mins . ' دقيقة';
    }
    
    return $duration;
}

/**
 * تسجيل دخول المستخدم
 * 
 * @param mysqli $conn اتصال قاعدة البيانات
 * @param string $email البريد الإلكتروني
 * @param string $password كلمة المرور
 * @return array|bool معلومات المستخدم إذا نجح تسجيل الدخول، أو false في حالة الفشل
 */
function loginUser($conn, $email, $password) {
    $sql = "SELECT * FROM users WHERE email = ?";
    $stmt = $conn->prepare($sql);
    $stmt->bind_param('s', $email);
    $stmt->execute();
    $result = $stmt->get_result();
    
    if ($result->num_rows > 0) {
        $user = $result->fetch_assoc();
        
        // التحقق من كلمة المرور
        if (password_verify($password, $user['password_hash'])) {
            // عدم إرجاع كلمة المرور المشفرة
            unset($user['password_hash']);
            return $user;
        }
    }
    
    return false;
}

/**
 * تسجيل مستخدم جديد
 * 
 * @param mysqli $conn اتصال قاعدة البيانات
 * @param array $user_data بيانات المستخدم
 * @return array|bool معلومات المستخدم إذا نجح التسجيل، أو false في حالة الفشل
 */
function registerUser($conn, $user_data) {
    // التحقق من عدم وجود البريد الإلكتروني مسبقًا
    $sql = "SELECT id FROM users WHERE email = ?";
    $stmt = $conn->prepare($sql);
    $stmt->bind_param('s', $user_data['email']);
    $stmt->execute();
    $result = $stmt->get_result();
    
    if ($result->num_rows > 0) {
        return false; // البريد الإلكتروني موجود بالفعل
    }
    
    // التحقق من عدم وجود اسم المستخدم مسبقًا
    $sql = "SELECT id FROM users WHERE username = ?";
    $stmt = $conn->prepare($sql);
    $stmt->bind_param('s', $user_data['username']);
    $stmt->execute();
    $result = $stmt->get_result();
    
    if ($result->num_rows > 0) {
        return false; // اسم المستخدم موجود بالفعل
    }
    
    // تشفير كلمة المرور
    $password_hash = password_hash($user_data['password'], PASSWORD_DEFAULT);
    
    // إضافة المستخدم الجديد
    $sql = "INSERT INTO users (
                username, email, password_hash, first_name, last_name,
                date_of_birth, created_at
            ) VALUES (?, ?, ?, ?, ?, ?, NOW())";
            
    $stmt = $conn->prepare($sql);
    $stmt->bind_param(
        'ssssss',
        $user_data['username'],
        $user_data['email'],
        $password_hash,
        $user_data['first_name'],
        $user_data['last_name'],
        $user_data['date_of_birth']
    );
    
    if ($stmt->execute()) {
        $user_id = $conn->insert_id;
        
        // الحصول على معلومات المستخدم بعد التسجيل
        $sql = "SELECT * FROM users WHERE id = ?";
        $stmt = $conn->prepare($sql);
        $stmt->bind_param('i', $user_id);
        $stmt->execute();
        $result = $stmt->get_result();
        
        if ($result->num_rows > 0) {
            $user = $result->fetch_assoc();
            
            // عدم إرجاع كلمة المرور المشفرة
            unset($user['password_hash']);
            return $user;
        }
    }
    
    return false;
}

/**
 * تحديث بيانات المستخدم
 * 
 * @param mysqli $conn اتصال قاعدة البيانات
 * @param int $user_id معرف المستخدم
 * @param array $user_data بيانات المستخدم المحدثة
 * @return bool نتيجة عملية التحديث
 */
function updateUserProfile($conn, $user_id, $user_data) {
    $sql = "UPDATE users SET
                first_name = ?,
                last_name = ?,
                date_of_birth = ?,
                nationality = ?,
                passport_number = ?,
                phone_number = ?
            WHERE id = ?";
            
    $stmt = $conn->prepare($sql);
    $stmt->bind_param(
        'ssssssi',
        $user_data['first_name'],
        $user_data['last_name'],
        $user_data['date_of_birth'],
        $user_data['nationality'],
        $user_data['passport_number'],
        $user_data['phone_number'],
        $user_id
    );
    
    return $stmt->execute();
}

/**
 * تحديث كلمة مرور المستخدم
 * 
 * @param mysqli $conn اتصال قاعدة البيانات
 * @param int $user_id معرف المستخدم
 * @param string $current_password كلمة المرور الحالية
 * @param string $new_password كلمة المرور الجديدة
 * @return bool نتيجة عملية التحديث
 */
function updateUserPassword($conn, $user_id, $current_password, $new_password) {
    // التحقق من كلمة المرور الحالية
    $sql = "SELECT password_hash FROM users WHERE id = ?";
    $stmt = $conn->prepare($sql);
    $stmt->bind_param('i', $user_id);
    $stmt->execute();
    $result = $stmt->get_result();
    
    if ($result->num_rows > 0) {
        $row = $result->fetch_assoc();
        
        if (password_verify($current_password, $row['password_hash'])) {
            // تشفير كلمة المرور الجديدة
            $new_password_hash = password_hash($new_password, PASSWORD_DEFAULT);
            
            // تحديث كلمة المرور
            $sql = "UPDATE users SET password_hash = ? WHERE id = ?";
            $stmt = $conn->prepare($sql);
            $stmt->bind_param('si', $new_password_hash, $user_id);
            
            return $stmt->execute();
        }
    }
    
    return false;
}

/**
 * الحصول على قائمة الدول
 * 
 * @return array مصفوفة تحتوي على قائمة الدول
 */
function getCountries() {
    // قائمة الدول
    return [
        'Afghanistan', 'Albania', 'Algeria', 'Andorra', 'Angola', 'Antigua and Barbuda', 'Argentina', 'Armenia', 'Australia', 'Austria',
        'Azerbaijan', 'Bahamas', 'Bahrain', 'Bangladesh', 'Barbados', 'Belarus', 'Belgium', 'Belize', 'Benin', 'Bhutan',
        'Bolivia', 'Bosnia and Herzegovina', 'Botswana', 'Brazil', 'Brunei', 'Bulgaria', 'Burkina Faso', 'Burundi', 'Cambodia', 'Cameroon',
        'Canada', 'Cape Verde', 'Central African Republic', 'Chad', 'Chile', 'China', 'Colombia', 'Comoros', 'Congo', 'Costa Rica',
        'Croatia', 'Cuba', 'Cyprus', 'Czech Republic', 'Denmark', 'Djibouti', 'Dominica', 'Dominican Republic', 'East Timor', 'Ecuador',
        'Egypt', 'El Salvador', 'Equatorial Guinea', 'Eritrea', 'Estonia', 'Ethiopia', 'Fiji', 'Finland', 'France', 'Gabon',
        'Gambia', 'Georgia', 'Germany', 'Ghana', 'Greece', 'Grenada', 'Guatemala', 'Guinea', 'Guinea-Bissau', 'Guyana',
        'Haiti', 'Honduras', 'Hungary', 'Iceland', 'India', 'Indonesia', 'Iran', 'Iraq', 'Ireland', 'Israel',
        'Italy', 'Ivory Coast', 'Jamaica', 'Japan', 'Jordan', 'Kazakhstan', 'Kenya', 'Kiribati', 'Korea, North', 'Korea, South',
        'Kuwait', 'Kyrgyzstan', 'Laos', 'Latvia', 'Lebanon', 'Lesotho', 'Liberia', 'Libya', 'Liechtenstein', 'Lithuania',
        'Luxembourg', 'Macedonia', 'Madagascar', 'Malawi', 'Malaysia', 'Maldives', 'Mali', 'Malta', 'Marshall Islands', 'Mauritania',
        'Mauritius', 'Mexico', 'Micronesia', 'Moldova', 'Monaco', 'Mongolia', 'Montenegro', 'Morocco', 'Mozambique', 'Myanmar',
        'Namibia', 'Nauru', 'Nepal', 'Netherlands', 'New Zealand', 'Nicaragua', 'Niger', 'Nigeria', 'Norway', 'Oman',
        'Pakistan', 'Palau', 'Palestine', 'Panama', 'Papua New Guinea', 'Paraguay', 'Peru', 'Philippines', 'Poland', 'Portugal',
        'Qatar', 'Romania', 'Russia', 'Rwanda', 'Saint Kitts and Nevis', 'Saint Lucia', 'Saint Vincent and the Grenadines', 'Samoa', 'San Marino', 'Sao Tome and Principe',
        'Saudi Arabia', 'Senegal', 'Serbia', 'Seychelles', 'Sierra Leone', 'Singapore', 'Slovakia', 'Slovenia', 'Solomon Islands', 'Somalia',
        'South Africa', 'South Sudan', 'Spain', 'Sri Lanka', 'Sudan', 'Suriname', 'Swaziland', 'Sweden', 'Switzerland', 'Syria',
        'Taiwan', 'Tajikistan', 'Tanzania', 'Thailand', 'Togo', 'Tonga', 'Trinidad and Tobago', 'Tunisia', 'Turkey', 'Turkmenistan',
        'Tuvalu', 'Uganda', 'Ukraine', 'United Arab Emirates', 'United Kingdom', 'United States', 'Uruguay', 'Uzbekistan', 'Vanuatu', 'Vatican City',
        'Venezuela', 'Vietnam', 'Yemen', 'Zambia', 'Zimbabwe'
    ];
}

/**
 * الحصول على الوجهات الشائعة
 * 
 * @param mysqli $conn اتصال قاعدة البيانات
 * @param int $limit عدد النتائج المطلوبة
 * @return array مصفوفة تحتوي على الوجهات الشائعة
 */
function getPopularDestinations($conn, $limit = 6) {
    $destinations = [];
    
    $sql = "SELECT c.id, c.name, c.country, c.airport_code, c.airport_name,
                   c.latitude, c.longitude, 
                   CONCAT('assets/destinations/', LOWER(REPLACE(c.name, ' ', '-')), '.jpg') as image_url
            FROM cities c
            WHERE c.popular_destination = 1
            ORDER BY c.id
            LIMIT ?";
            
    $stmt = $conn->prepare($sql);
    $stmt->bind_param('i', $limit);
    $stmt->execute();
    $result = $stmt->get_result();
    
    if ($result->num_rows > 0) {
        while ($row = $result->fetch_assoc()) {
            $destinations[] = $row;
        }
    }
    
    return $destinations;
}

/**
 * الحصول على العروض الخاصة
 * 
 * @param mysqli $conn اتصال قاعدة البيانات
 * @param int $limit عدد النتائج المطلوبة
 * @return array مصفوفة تحتوي على العروض الخاصة
 */
function getSpecialOffers($conn, $limit = 4) {
    $offers = [];
    
    $sql = "SELECT fs.id as flight_id, a.name as airline_name, a.logo_url,
                   fc.name as from_city_name, fc.country as from_country, fc.airport_code as from_airport_code,
                   tc.name as to_city_name, tc.country as to_country, tc.airport_code as to_airport_code,
                   fs.flight_number, fp.base_price,
                   CONCAT('assets/destinations/', LOWER(REPLACE(tc.name, ' ', '-')), '.jpg') as image_url
            FROM flight_schedules fs
            JOIN airlines a ON fs.airline_id = a.id
            JOIN cities fc ON fs.from_city_id = fc.id
            JOIN cities tc ON fs.to_city_id = tc.id
            LEFT JOIN flight_prices fp ON fs.id = fp.flight_schedule_id AND fp.travel_class = 'Economy'
            WHERE tc.popular_destination = 1
            ORDER BY RAND()
            LIMIT ?";
            
    $stmt = $conn->prepare($sql);
    $stmt->bind_param('i', $limit);
    $stmt->execute();
    $result = $stmt->get_result();
    
    if ($result->num_rows > 0) {
        while ($row = $result->fetch_assoc()) {
            // تعديل السعر لإنشاء عرض خاص
            $original_price = $row['base_price'] ?: 100;
            $discount_percentage = rand(15, 40);
            $discounted_price = round($original_price * (1 - $discount_percentage / 100));
            
            $offers[] = [
                'flight_id' => $row['flight_id'],
                'airline_name' => $row['airline_name'],
                'logo_url' => $row['logo_url'],
                'from_city_name' => $row['from_city_name'],
                'from_country' => $row['from_country'],
                'from_airport_code' => $row['from_airport_code'],
                'to_city_name' => $row['to_city_name'],
                'to_country' => $row['to_country'],
                'to_airport_code' => $row['to_airport_code'],
                'flight_number' => $row['flight_number'],
                'original_price' => $original_price,
                'discount_percentage' => $discount_percentage,
                'discounted_price' => $discounted_price,
                'currency' => 'USD',
                'image_url' => $row['image_url'],
                'valid_until' => date('Y-m-d', strtotime('+30 days'))
            ];
        }
    }
    
    return $offers;
}

// ===================================================
// معالجة طلبات المستخدم
// ===================================================

// معالجة تسجيل الدخول
if (isset($_POST['login'])) {
    $email = $_POST['email'];
    $password = $_POST['password'];
    
    $user = loginUser($conn, $email, $password);
    
    if ($user) {
        $_SESSION['user'] = $user;
        
        // إعادة التوجيه إلى الصفحة الرئيسية أو الصفحة التي كان المستخدم يتصفحها
        $redirect_url = isset($_SESSION['redirect_after_login']) ? $_SESSION['redirect_after_login'] : 'index.php';
        unset($_SESSION['redirect_after_login']);
        
        header("Location: $redirect_url");
        exit;
    } else {
        $login_error = "البريد الإلكتروني أو كلمة المرور غير صحيحة";
    }
}

// معالجة تسجيل مستخدم جديد
if (isset($_POST['register'])) {
    // التحقق من تطابق كلمتي المرور
    if ($_POST['password'] !== $_POST['confirm_password']) {
        $register_error = "كلمتا المرور غير متطابقتين";
    } else {
        $user_data = [
            'username' => $_POST['username'],
            'email' => $_POST['email'],
            'password' => $_POST['password'],
            'first_name' => $_POST['first_name'],
            'last_name' => $_POST['last_name'],
            'date_of_birth' => !empty($_POST['date_of_birth']) ? $_POST['date_of_birth'] : null
        ];
        
        $user = registerUser($conn, $user_data);
        
        if ($user) {
            $_SESSION['user'] = $user;
            $_SESSION['register_success'] = true;
            
            header("Location: index.php");
            exit;
        } else {
            $register_error = "البريد الإلكتروني أو اسم المستخدم موجود بالفعل";
        }
    }
}

// معالجة تسجيل الخروج
if (isset($_GET['logout'])) {
    unset($_SESSION['user']);
    
    header("Location: index.php");
    exit;
}

// التحقق مما إذا كانت الصفحة تتطلب تسجيل الدخول
if (in_array($page, $protected_pages) && !isset($_SESSION['user'])) {
    // حفظ الصفحة الحالية لإعادة التوجيه بعد تسجيل الدخول
    $_SESSION['redirect_after_login'] = $_SERVER['REQUEST_URI'];
    
    $page = 'login';
}

// ===================================================
// بداية الخرج HTML
// ===================================================
?>
<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>نظام حجز الطيران - الموقع الأمثل لحجز تذاكر السفر</title>

    <!-- Bootstrap RTL CSS -->
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.rtl.min.css">
    <!-- Font Awesome -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <!-- Custom CSS -->
    <style>
        :root {
            --primary-color: #1a237e;
            --secondary-color: #0d47a1;
            --accent-color: #42a5f5;
            --bg-dark: #121212;
            --bg-dark-light: #1e1e1e;
            --text-light: #f5f5f5;
            --card-bg: #212121;
            --input-bg: #2c2c2c;
            --border-color: #333;
        }
        
        body {
            background-color: var(--bg-dark);
            color: var(--text-light);
            font-family: 'Tajawal', sans-serif;
        }
        
        /* ===== الرأس وشريط التنقل ===== */
        .navbar {
            background-color: var(--bg-dark);
            border-bottom: 1px solid var(--border-color);
            padding: 15px 0;
        }
        
        .navbar-brand img {
            height: 40px;
        }
        
        .navbar-dark .navbar-nav .nav-link {
            color: var(--text-light);
            margin: 0 10px;
            position: relative;
            transition: all 0.3s;
        }
        
        .navbar-dark .navbar-nav .nav-link:hover,
        .navbar-dark .navbar-nav .nav-link.active {
            color: var(--accent-color);
        }
        
        .navbar-dark .navbar-nav .nav-link::after {
            content: '';
            position: absolute;
            bottom: -5px;
            left: 0;
            width: 0;
            height: 2px;
            background-color: var(--accent-color);
            transition: width 0.3s;
        }
        
        .navbar-dark .navbar-nav .nav-link:hover::after,
        .navbar-dark .navbar-nav .nav-link.active::after {
            width: 100%;
        }
        
        .search-toggler {
            background-color: var(--primary-color);
            color: white;
            border-radius: 50%;
            width: 40px;
            height: 40px;
            display: flex;
            align-items: center;
            justify-content: center;
            margin-left: 15px;
            transition: all 0.3s;
        }
        
        .search-toggler:hover {
            background-color: var(--secondary-color);
            transform: scale(1.1);
        }
        
        .user-dropdown .dropdown-menu {
            background-color: var(--bg-dark-light);
            border: 1px solid var(--border-color);
            margin-top: 15px;
        }
        
        .user-dropdown .dropdown-item {
            color: var(--text-light);
            padding: 10px 15px;
        }
        
        .user-dropdown .dropdown-item:hover {
            background-color: var(--primary-color);
            color: white;
        }
        
        .user-dropdown .dropdown-divider {
            border-top: 1px solid var(--border-color);
        }
        
        /* ===== الروابط والأزرار ===== */
        a {
            color: var(--accent-color);
            text-decoration: none;
        }
        
        a:hover {
            color: #90caf9;
        }
        
        .btn-primary {
            background-color: var(--primary-color);
            border-color: var(--primary-color);
            transition: all 0.3s;
        }
        
        .btn-primary:hover {
            background-color: var(--secondary-color);
            border-color: var(--secondary-color);
            transform: translateY(-2px);
            box-shadow: 0 5px 15px rgba(0, 0, 0, 0.3);
        }
        
        .btn-secondary {
            background-color: var(--secondary-color);
            border-color: var(--secondary-color);
        }
        
        .btn-secondary:hover {
            background-color: #1565c0;
            border-color: #1565c0;
        }
        
        .btn-outline-primary {
            color: var(--accent-color);
            border-color: var(--accent-color);
        }
        
        .btn-outline-primary:hover {
            background-color: var(--accent-color);
            color: white;
        }
        
        /* ===== البطاقات والحاويات ===== */
        .card {
            background-color: var(--card-bg);
            border: 1px solid var(--border-color);
            border-radius: 10px;
            overflow: hidden;
            box-shadow: 0 5px 15px rgba(0, 0, 0, 0.3);
            transition: transform 0.3s;
        }
        
        .card:hover {
            transform: translateY(-5px);
        }
        
        .card-header {
            border-bottom: 1px solid var(--border-color);
            padding: 15px 20px;
        }
        
        .card-footer {
            border-top: 1px solid var(--border-color);
        }
        
        /* ===== تذييل الصفحة ===== */
        footer {
            background-color: var(--bg-dark);
            border-top: 1px solid var(--border-color);
            padding: 40px 0 20px;
            margin-top: 50px;
        }
        
        .footer-heading {
            color: white;
            font-size: 1.2rem;
            margin-bottom: 20px;
            position: relative;
            padding-right: 15px;
        }
        
        .footer-heading::before {
            content: '';
            position: absolute;
            right: 0;
            top: 50%;
            transform: translateY(-50%);
            width: 5px;
            height: 20px;
            background-color: var(--accent-color);
        }
        
        .footer-links {
            list-style: none;
            padding: 0;
        }
        
        .footer-links li {
            margin-bottom: 10px;
        }
        
        .footer-links a {
            color: #bdbdbd;
            transition: all 0.3s;
        }
        
        .footer-links a:hover {
            color: var(--accent-color);
            padding-right: 5px;
        }
        
        .social-icons a {
            display: inline-block;
            width: 40px;
            height: 40px;
            background-color: #333;
            color: white;
            border-radius: 50%;
            text-align: center;
            line-height: 40px;
            margin-right: 10px;
            transition: all 0.3s;
        }
        
        .social-icons a:hover {
            background-color: var(--accent-color);
            transform: translateY(-5px);
        }
        
        .footer-bottom {
            border-top: 1px solid var(--border-color);
            padding-top: 20px;
            margin-top: 40px;
            text-align: center;
            color: #bdbdbd;
        }
        
        /* ===== نمط المدخلات ===== */
        .form-control, .form-select {
            background-color: var(--input-bg);
            border: 1px solid var(--border-color);
            color: var(--text-light);
            padding: 10px 15px;
        }
        
        .form-control:focus, .form-select:focus {
            background-color: var(--input-bg);
            color: var(--text-light);
            border-color: var(--accent-color);
            box-shadow: 0 0 0 0.25rem rgba(66, 165, 245, 0.25);
        }
        
        /* ===== تأثيرات الغيوم المتحركة ===== */
        .clouds-container {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            pointer-events: none;
            z-index: -1;
            overflow: hidden;
        }
        
        .cloud {
            position: absolute;
            width: 200px;
            height: 60px;
            background-color: rgba(255, 255, 255, 0.05);
            border-radius: 50px;
            filter: blur(10px);
            animation: cloud-move linear infinite;
        }
        
        .cloud::before,
        .cloud::after {
            content: '';
            position: absolute;
            background-color: rgba(255, 255, 255, 0.05);
            border-radius: 50%;
        }
        
        .cloud::before {
            width: 100px;
            height: 100px;
            top: -50px;
            left: 25px;
        }
        
        .cloud::after {
            width: 80px;
            height: 80px;
            top: -35px;
            right: 25px;
        }
        
        .cloud:nth-child(1) {
            top: 10%;
            animation-duration: 60s;
        }
        
        .cloud:nth-child(2) {
            top: 30%;
            animation-duration: 75s;
            animation-delay: -10s;
        }
        
        .cloud:nth-child(3) {
            top: 50%;
            animation-duration: 45s;
            animation-delay: -20s;
        }
        
        .cloud:nth-child(4) {
            top: 70%;
            animation-duration: 90s;
            animation-delay: -5s;
        }
        
        .cloud:nth-child(5) {
            top: 85%;
            animation-duration: 70s;
            animation-delay: -30s;
        }
        
        @keyframes cloud-move {
            0% {
                right: -200px;
                opacity: 0.3;
            }
            5% {
                opacity: 0.5;
            }
            95% {
                opacity: 0.5;
            }
            100% {
                right: calc(100% + 200px);
                opacity: 0.3;
            }
        }
        
        /* ===== Airplane Animation ===== */
        .airplane-container {
            position: fixed;
            top: 20%;
            left: -150px;
            z-index: -1;
            pointer-events: none;
            animation: fly-through 45s linear infinite;
        }
        
        .airplane {
            position: relative;
            transform: rotate(20deg);
        }
        
        .airplane i {
            font-size: 50px;
            color: rgba(255, 255, 255, 0.2);
        }
        
        .airplane-trail {
            position: absolute;
            top: 60%;
            right: 0;
            width: 100px;
            height: 3px;
            background: linear-gradient(to left, rgba(255, 255, 255, 0), rgba(255, 255, 255, 0.2));
        }
        
        @keyframes fly-through {
            0% {
                left: -150px;
                top: 20%;
            }
            100% {
                left: calc(100% + 150px);
                top: 60%;
            }
        }
        
        @media (max-width: 768px) {
            .cloud, .airplane-container {
                display: none;
            }
        }
        
        /* ===== طبقة Preloader ===== */
        .preloader {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background-color: var(--bg-dark);
            z-index: 9999;
            display: flex;
            justify-content: center;
            align-items: center;
            transition: opacity 0.5s ease-in-out;
        }
        
        .preloader.fade-out {
            opacity: 0;
            pointer-events: none;
        }
        
        .plane-loader {
            width: 150px;
            height: 150px;
            position: relative;
        }
        
        .plane-loader i {
            font-size: 80px;
            color: var(--accent-color);
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            animation: plane-rotate 2.5s ease-in-out infinite;
        }
        
        @keyframes plane-rotate {
            0% {
                transform: translate(-50%, -50%) rotate(0deg);
            }
            25% {
                transform: translate(-50%, -50%) rotate(90deg);
            }
            50% {
                transform: translate(-50%, -50%) rotate(180deg);
            }
            75% {
                transform: translate(-50%, -50%) rotate(270deg);
            }
            100% {
                transform: translate(-50%, -50%) rotate(360deg);
            }
        }
        
        /* ===== تأثيرات المؤثرات الضوئية ===== */
        .light-effect {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            pointer-events: none;
            z-index: -2;
            opacity: 0.15;
        }
        
        .light-effect div {
            position: absolute;
            width: 300px;
            height: 300px;
            border-radius: 50%;
            filter: blur(100px);
        }
        
        .light-effect div:nth-child(1) {
            top: 0;
            left: 10%;
            background-color: var(--primary-color);
            animation: light-pulse 8s ease-in-out infinite;
        }
        
        .light-effect div:nth-child(2) {
            bottom: 10%;
            right: 10%;
            background-color: var(--secondary-color);
            animation: light-pulse 8s ease-in-out infinite 1s;
        }
        
        .light-effect div:nth-child(3) {
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            background-color: var(--accent-color);
            animation: light-pulse 8s ease-in-out infinite 2s;
        }
        
        @keyframes light-pulse {
            0% {
                opacity: 0.2;
                transform: scale(1);
            }
            50% {
                opacity: 0.5;
                transform: scale(1.2);
            }
            100% {
                opacity: 0.2;
                transform: scale(1);
            }
        }
        
        /* تنسيق قسم العروض */
        .offer-card {
            height: 100%;
        }

        .offer-card-img {
            position: relative;
            overflow: hidden;
        }

        .offer-card-img img {
            height: 150px;
            object-fit: cover;
            transition: transform 0.5s;
        }

        .offer-card:hover .offer-card-img img {
            transform: scale(1.1);
        }

        .offer-discount {
            position: absolute;
            top: 10px;
            left: 10px;
            background-color: rgba(255, 0, 0, 0.8);
            color: white;
            padding: 5px 10px;
            border-radius: 20px;
            font-weight: bold;
            font-size: 0.9rem;
        }

        .airline-logo {
            width: 30px;
            height: 30px;
            object-fit: contain;
        }

        .price-info {
            margin-bottom: 5px;
        }

        .original-price {
            text-decoration: line-through;
            color: #dc3545;
            margin-right: 10px;
            font-size: 0.9rem;
        }

        .current-price {
            font-weight: bold;
            color: #28a745;
            font-size: 1.2rem;
        }

        /* تنسيق قسم الوجهات */
        .destination-card {
            position: relative;
            overflow: hidden;
            height: 200px;
        }

        .destination-card img {
            height: 100%;
            object-fit: cover;
            transition: transform 0.5s;
        }

        .destination-card:hover img {
            transform: scale(1.1);
        }

        .destination-card-overlay {
            position: absolute;
            bottom: 0;
            left: 0;
            right: 0;
            padding: 20px;
            background: linear-gradient(to top, rgba(0, 0, 0, 0.8), transparent);
            color: white;
            transition: all 0.3s;
        }

        .destination-card:hover .destination-card-overlay {
            background: linear-gradient(to top, rgba(0, 0, 0, 0.9), rgba(0, 0, 0, 0.5));
        }

        .destination-card-overlay h5 {
            margin-bottom: 5px;
        }

        .destination-card-overlay .btn {
            opacity: 0;
            transform: translateY(20px);
            transition: all 0.3s;
        }

        .destination-card:hover .destination-card-overlay .btn {
            opacity: 1;
            transform: translateY(0);
        }

        /* تنسيق قسم المميزات */
        .feature-card {
            padding: 20px;
            height: 100%;
            background-color: var(--card-bg);
            border-radius: 10px;
            box-shadow: 0 5px 15px rgba(0, 0, 0, 0.1);
            transition: transform 0.3s;
        }

        .feature-card:hover {
            transform: translateY(-10px);
        }

        .feature-icon {
            display: inline-block;
            width: 80px;
            height: 80px;
            line-height: 80px;
            text-align: center;
            border-radius: 50%;
            background-color: var(--bg-dark-light);
            margin-bottom: 20px;
            transition: all 0.3s;
        }

        .feature-card:hover .feature-icon {
            background-color: var(--primary-color);
            color: white !important;
        }

        .feature-card:hover .feature-icon i {
            color: white !important;
        }

        /* تنسيق الأسئلة الشائعة */
        .accordion-item {
            background-color: var(--card-bg);
            border: 1px solid var(--border-color);
            margin-bottom: 10px;
        }

        .accordion-button {
            background-color: var(--card-bg);
            color: var(--text-light);
            padding: 15px 20px;
            font-weight: 500;
        }

        .accordion-button:not(.collapsed) {
            background-color: var(--primary-color);
            color: white;
        }

        .accordion-button:focus {
            box-shadow: none;
            border-color: var(--border-color);
        }

        .accordion-button::after {
            background-image: url("data:image/svg+xml,%3csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 16 16' fill='%23ffffff'%3e%3cpath fill-rule='evenodd' d='M1.646 4.646a.5.5 0 0 1 .708 0L8 10.293l5.646-5.647a.5.5 0 0 1 .708.708l-6 6a.5.5 0 0 1-.708 0l-6-6a.5.5 0 0 1 0-.708z'/%3e%3c/svg%3e");
        }

        .accordion-body {
            padding: 15px 20px;
        }
        
        /* صفحة 404 */
        .error-container {
            padding: 50px 0;
            position: relative;
        }

        .error-code {
            font-size: 150px;
            font-weight: bold;
            color: var(--primary-color);
            opacity: 0.2;
            position: relative;
            z-index: 1;
        }

        .error-plane {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            z-index: 2;
            animation: plane-move 6s ease-in-out infinite;
        }

        .error-plane i {
            font-size: 80px;
            color: var(--accent-color);
            transform: rotate(-45deg);
        }

        @keyframes plane-move {
            0% {
                transform: translate(-50%, -50%) translateX(-50px) translateY(-20px);
            }
            50% {
                transform: translate(-50%, -50%) translateX(50px) translateY(20px);
            }
            100% {
                transform: translate(-50%, -50%) translateX(-50px) translateY(-20px);
            }
        }
        
        /* تنسيق صفحة تسجيل الدخول للرحلة */
        .seat-selection-container {
            padding: 20px;
        }

        .seat-map {
            margin-bottom: 20px;
        }

        .airplane-header {
            width: 60px;
            height: 60px;
            background-color: var(--primary-color);
            color: white;
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            margin: 0 auto;
        }

        .airplane-header i {
            font-size: 30px;
            transform: rotate(90deg);
        }

        .seats-row {
            display: flex;
            justify-content: space-between;
        }

        .seat-column {
            width: 40px;
            text-align: center;
        }

        .seat-column.empty {
            width: 20px;
        }

        .seat {
            display: inline-block;
            width: 35px;
            height: 35px;
            border: 1px solid #ccc;
            border-radius: 5px;
            text-align: center;
            line-height: 35px;
            cursor: pointer;
            background-color: #e9ecef;
            font-size: 0.8rem;
            transition: all 0.2s ease;
        }

        input[type="radio"]:checked + .seat {
            background-color: var(--secondary-color);
            color: white;
            border-color: var(--secondary-color);
        }

        .seat.occupied {
            background-color: #dc3545;
            color: white;
            border-color: #dc3545;
            cursor: not-allowed;
        }

        .seat.business {
            background-color: #6c757d;
            color: white;
            border-color: #6c757d;
        }

        .seat.exit-row {
            border-color: #fd7e14;
            border-width: 2px;
        }

        .seat.unavailable {
            background-color: #f8f9fa;
            color: #adb5bd;
            cursor: not-allowed;
        }

        input[type="radio"] {
            display: none;
        }

        .row-number {
            display: flex;
            align-items: center;
            justify-content: center;
            font-weight: bold;
        }

        .cabin-divider {
            position: relative;
            text-align: center;
            margin: 15px 0;
        }

        .divider-line {
            height: 2px;
            background-color: #ccc;
            margin: 10px 0;
        }

        .divider-text {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            background-color: var(--card-bg);
            padding: 0 10px;
            font-weight: bold;
            font-size: 0.9rem;
        }

        .exit-row-indicator {
            text-align: center;
            font-size: 0.8rem;
            font-weight: bold;
            color: #fd7e14;
            margin: 5px 0;
        }

        .seat-legend {
            display: flex;
            justify-content: space-between;
            margin-top: 10px;
        }

        .legend-item {
            display: flex;
            align-items: center;
            margin-right: 15px;
            font-size: 0.8rem;
        }

        .seat-sample {
            width: 20px;
            height: 20px;
            border-radius: 3px;
            margin-right: 5px;
        }

        .seat-sample.available {
            background-color: #e9ecef;
            border: 1px solid #ccc;
        }

        .seat-sample.selected {
            background-color: var(--secondary-color);
            border: 1px solid var(--secondary-color);
        }

        .seat-sample.occupied {
            background-color: #dc3545;
            border: 1px solid #dc3545;
        }

        .seat-sample.business {
            background-color: #6c757d;
            border: 1px solid #6c757d;
        }

        .boarding-info {
            padding: 10px;
        }

        .info-label {
            font-size: 0.8rem;
            color: #6c757d;
        }

        .info-value {
            font-size: 1.2rem;
            font-weight: bold;
        }
        
        /* تنسيق بطاقة الصعود */
        .boarding-pass {
            background-color: white;
            border-radius: 10px;
            overflow: hidden;
            box-shadow: 0 5px 15px rgba(0, 0, 0, 0.1);
            margin-bottom: 20px;
        }

        .pass-header {
            display: flex;
            align-items: center;
            padding: 20px;
            border-bottom: 1px dashed #ddd;
        }

        .airline-logo {
            max-width: 150px;
            margin-right: 20px;
        }

        .airline-logo img {
            max-height: 50px;
        }

        .pass-title {
            flex-grow: 1;
        }

        .pass-title h5 {
            margin: 0;
            color: var(--primary-color);
        }

        .airline-name {
            color: #6c757d;
        }

        .pass-body {
            padding: 20px;
            border-bottom: 1px dashed #ddd;
        }

        .pass-footer {
            padding: 20px;
            background-color: #f8f9fa;
        }

        .info-label {
            font-size: 0.8rem;
            color: #6c757d;
            margin-bottom: 5px;
        }

        .info-value {
            font-weight: bold;
            font-size: 1.2rem;
        }

        .airport-code {
            font-size: 1.5rem;
            font-weight: bold;
            color: var(--primary-color);
        }

        .airport-name {
            font-size: 0.9rem;
        }

        .flight-direction {
            position: relative;
            height: 40px;
            margin-bottom: 10px;
        }

        .flight-direction:before {
            content: '';
            position: absolute;
            top: 50%;
            left: 0;
            right: 0;
            height: 2px;
            background-color: #ddd;
            z-index: 1;
        }

        .flight-arrow {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            width: 40px;
            height: 40px;
            background-color: var(--secondary-color);
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            color: white;
            z-index: 2;
        }

        .flight-arrow i {
            transform: rotate(90deg);
        }

        .flight-number, .flight-date {
            font-size: 0.9rem;
        }

        .time-value, .duration-value {
            font-weight: bold;
            font-size: 1.2rem;
        }

        .boarding-info {
            border-left: 1px dashed #ddd;
            height: 100%;
            padding-left: 20px;
        }

        .gate-value, .seat-value {
            font-size: 1.5rem;
            font-weight: bold;
            color: var(--primary-color);
        }

        .class-value, .boarding-time {
            font-size: 1.2rem;
            font-weight: bold;
        }

        .barcode {
            margin-top: 20px;
        }

        .barcode-img {
            margin-bottom: 5px;
        }

        .qr-code {
            margin: 0 auto;
            width: 100px;
            height: 100px;
        }

        @media print {
            body {
                background-color: white;
                color: black;
            }
            
            .btn, .alert, nav, footer, .card-header, .no-print {
                display: none !important;
            }
            
            .card {
                border: none;
                box-shadow: none;
            }
            
            .container {
                width: 100%;
                max-width: 100%;
                padding: 0;
            }
            
            .boarding-pass {
                box-shadow: none;
                border: 1px solid #ddd;
            }
        }
        
        /* تنسيق صفحة البحث */
        .flight-card {
            margin-bottom: 20px;
        }

        .flight-card .card-body {
            padding: 20px;
        }

        .flight-direction-line {
            position: relative;
            height: 2px;
            background-color: #ddd;
            margin: 15px 0;
        }

        .flight-direction-icon {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            width: 30px;
            height: 30px;
            background-color: var(--secondary-color);
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            color: white;
        }

        .flight-details-toggler {
            cursor: pointer;
            color: var(--accent-color);
            padding: 5px 0;
            font-weight: 500;
        }

        .flight-details-toggler:hover {
            color: var(--primary-color);
        }

        .flight-info-label {
            font-size: 0.85rem;
            color: #6c757d;
            margin-bottom: 2px;
        }

        .flight-info-value {
            font-weight: 600;
        }

        .flight-time {
            font-size: 1.5rem;
            font-weight: bold;
        }

        .flight-date {
            font-size: 0.85rem;
            color: #6c757d;
        }

        .flight-city {
            font-weight: 500;
        }

        .flight-airport {
            font-size: 0.85rem;
            color: #6c757d;
        }

        .flight-duration {
            text-align: center;
            font-size: 0.9rem;
        }

        .flight-stops {
            text-align: center;
            font-size: 0.8rem;
            color: #6c757d;
        }

        .price-tag {
            text-align: center;
            padding: 15px;
            background-color: rgba(66, 165, 245, 0.1);
            border-radius: 8px;
        }

        .price-amount {
            font-size: 1.8rem;
            font-weight: bold;
            color: var(--primary-color);
        }

        .price-label {
            font-size: 0.8rem;
            color: #6c757d;
            margin-bottom: 10px;
        }

        .flight-amenities {
            display: flex;
            flex-wrap: wrap;
            margin-top: 10px;
        }

        .amenity-item {
            font-size: 0.8rem;
            color: #6c757d;
            margin-right: 10px;
            margin-bottom: 5px;
        }
        
        /* Google Fonts - Tajawal */
        @import url('https://fonts.googleapis.com/css2?family=Tajawal:wght@300;400;500;700;800&display=swap');
    </style>
</head>
<body>
    <!-- Preloader -->
    <div class="preloader">
        <div class="plane-loader">
            <i class="fas fa-plane"></i>
        </div>
    </div>
    
    <!-- تأثيرات خلفية الغيوم -->
    <div class="clouds-container">
        <div class="cloud"></div>
        <div class="cloud"></div>
        <div class="cloud"></div>
        <div class="cloud"></div>
        <div class="cloud"></div>
    </div>
    
    <!-- تأثير الطائرة -->
    <div class="airplane-container">
        <div class="airplane">
            <i class="fas fa-plane"></i>
            <div class="airplane-trail"></div>
        </div>
    </div>
    
    <!-- تأثيرات ضوئية -->
    <div class="light-effect">
        <div></div>
        <div></div>
        <div></div>
    </div>
    
    <!-- شريط التنقل -->
    <nav class="navbar navbar-expand-lg navbar-dark">
        <div class="container">
            <a class="navbar-brand" href="all_in_one_flight_booking.php">
                <img src="assets/logo.png" alt="شعار حجز الطيران" onerror="this.src='https://via.placeholder.com/180x50/1a237e/ffffff?text=أجنحة+السفر'">
            </a>
            
            <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarMain">
                <span class="navbar-toggler-icon"></span>
            </button>
            
            <div class="collapse navbar-collapse" id="navbarMain">
                <ul class="navbar-nav me-auto mb-2 mb-lg-0">
                    <li class="nav-item">
                        <a class="nav-link <?php echo $page === 'home' ? 'active' : ''; ?>" href="all_in_one_flight_booking.php">الرئيسية</a>
                    </li>
                    <li class="nav-item">
                        <a class="nav-link <?php echo $page === 'search_flights' ? 'active' : ''; ?>" href="all_in_one_flight_booking.php?page=search_flights">الرحلات</a>
                    </li>
                    <li class="nav-item">
                        <a class="nav-link" href="#">العروض</a>
                    </li>
                    <li class="nav-item">
                        <a class="nav-link" href="#">خدماتنا</a>
                    </li>
                    <li class="nav-item">
                        <a class="nav-link <?php echo $page === 'booking_lookup' ? 'active' : ''; ?>" href="all_in_one_flight_booking.php?page=booking_lookup">تفاصيل الحجز</a>
                    </li>
                    <li class="nav-item">
                        <a class="nav-link <?php echo $page === 'check_in' ? 'active' : ''; ?>" href="all_in_one_flight_booking.php?page=check_in">تسجيل الدخول للرحلة</a>
                    </li>
                </ul>
                
                <div class="d-flex align-items-center">
                    <?php if (isset($_SESSION['user'])): ?>
                        <!-- قائمة المستخدم -->
                        <div class="dropdown user-dropdown">
                            <button class="btn btn-outline-light dropdown-toggle" type="button" id="userDropdown" data-bs-toggle="dropdown" aria-expanded="false">
                                <i class="fas fa-user-circle me-2"></i><?php echo $_SESSION['user']['username']; ?>
                            </button>
                            <ul class="dropdown-menu dropdown-menu-end" aria-labelledby="userDropdown">
                                <li><a class="dropdown-item" href="all_in_one_flight_booking.php?page=profile"><i class="fas fa-user me-2"></i>الملف الشخصي</a></li>
                                <li><a class="dropdown-item" href="all_in_one_flight_booking.php?page=my_bookings"><i class="fas fa-ticket-alt me-2"></i>حجوزاتي</a></li>
                                <li><hr class="dropdown-divider"></li>
                                <li><a class="dropdown-item" href="all_in_one_flight_booking.php?logout=1"><i class="fas fa-sign-out-alt me-2"></i>تسجيل الخروج</a></li>
                            </ul>
                        </div>
                    <?php else: ?>
                        <!-- أزرار تسجيل الدخول وإنشاء حساب جديد -->
                        <a href="all_in_one_flight_booking.php?page=login" class="btn btn-outline-light me-2">تسجيل الدخول</a>
                        <a href="all_in_one_flight_booking.php?page=register" class="btn btn-primary">إنشاء حساب</a>
                    <?php endif; ?>
                </div>
            </div>
        </div>
    </nav>

    <!-- محتوى الصفحة -->
    <main id="main-content">
        <?php
        // عرض رسالة نجاح التسجيل إذا كان المستخدم قد سجل للتو
        if (isset($_SESSION['register_success'])) {
            echo '<div class="alert alert-success alert-dismissible fade show" role="alert">
                    تم تسجيل حسابك بنجاح! مرحبًا بك في موقعنا.
                    <button type="button" class="btn-close" data-bs-dismiss="alert" aria-label="Close"></button>
                  </div>';
            
            unset($_SESSION['register_success']);
        }
        
        // عرض محتوى الصفحة بناءً على $page
        switch ($page) {
            case 'home':
                // الصفحة الرئيسية
                $popular_destinations = getPopularDestinations($conn);
                $special_offers = getSpecialOffers($conn);
                include 'pages/home.php';
                break;
                
            case 'login':
                // صفحة تسجيل الدخول
                include 'pages/login.php';
                break;
                
            case 'register':
                // صفحة إنشاء حساب جديد
                include 'pages/register.php';
                break;
                
            case 'search_flights':
                // صفحة البحث عن رحلات
                include 'pages/search_flights.php';
                break;
                
            case 'flight_details':
                // صفحة تفاصيل الرحلة
                include 'pages/flight_details.php';
                break;
                
            case 'book_flight':
                // صفحة حجز رحلة
                include 'pages/book_flight.php';
                break;
                
            case 'payment':
                // صفحة الدفع
                include 'pages/payment.php';
                break;
                
            case 'booking_confirmation':
                // صفحة تأكيد الحجز
                include 'pages/booking_confirmation.php';
                break;
                
            case 'booking_lookup':
                // صفحة البحث عن حجز
                include 'pages/booking_lookup.php';
                break;
                
            case 'check_in':
                // صفحة تسجيل الدخول للرحلة
                include 'pages/check_in.php';
                break;
                
            case 'boarding_pass':
                // صفحة بطاقة الصعود
                include 'pages/boarding_pass.php';
                break;
                
            case 'profile':
                // صفحة الملف الشخصي
                include 'pages/profile.php';
                break;
                
            case 'my_bookings':
                // صفحة حجوزاتي
                include 'pages/my_bookings.php';
                break;
                
            default:
                // صفحة 404 - الصفحة غير موجودة
                ?>
                <div class="container py-5">
                    <div class="row justify-content-center">
                        <div class="col-md-8 text-center">
                            <div class="error-container">
                                <div class="error-code">404</div>
                                <div class="error-plane">
                                    <i class="fas fa-plane"></i>
                                </div>
                                <h1 class="mb-4">عذراً، الصفحة غير موجودة!</h1>
                                <p class="lead mb-4">ربما تكون قد أخطأت في كتابة العنوان أو قد تكون الصفحة قد تم نقلها.</p>
                                <div class="mt-5">
                                    <a href="all_in_one_flight_booking.php" class="btn btn-primary btn-lg me-2">
                                        <i class="fas fa-home me-2"></i>العودة إلى الصفحة الرئيسية
                                    </a>
                                    <a href="all_in_one_flight_booking.php?page=search_flights" class="btn btn-outline-primary btn-lg">
                                        <i class="fas fa-search me-2"></i>البحث عن رحلات
                                    </a>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
                <?php
                break;
        }
        ?>
    </main>

    <!-- تذييل الصفحة -->
    <footer>
        <div class="container">
            <div class="row">
                <div class="col-md-3 col-sm-6 mb-4">
                    <h4 class="footer-heading">حول الموقع</h4>
                    <p class="text-muted">
                        موقع حجز الطيران يقدم أفضل العروض والأسعار لرحلات الطيران حول العالم. نعمل مع أكثر من 500 شركة طيران لتقديم أسعار منافسة وخدمة مميزة.
                    </p>
                    <div class="social-icons mt-3">
                        <a href="#"><i class="fab fa-facebook-f"></i></a>
                        <a href="#"><i class="fab fa-twitter"></i></a>
                        <a href="#"><i class="fab fa-instagram"></i></a>
                        <a href="#"><i class="fab fa-linkedin-in"></i></a>
                    </div>
                </div>
                
                <div class="col-md-3 col-sm-6 mb-4">
                    <h4 class="footer-heading">خدماتنا</h4>
                    <ul class="footer-links">
                        <li><a href="#">حجز تذاكر الطيران</a></li>
                        <li><a href="#">حجز الفنادق</a></li>
                        <li><a href="#">تأجير السيارات</a></li>
                        <li><a href="#">الرحلات السياحية</a></li>
                        <li><a href="#">التأمين على السفر</a></li>
                    </ul>
                </div>
                
                <div class="col-md-3 col-sm-6 mb-4">
                    <h4 class="footer-heading">روابط مهمة</h4>
                    <ul class="footer-links">
                        <li><a href="#">من نحن</a></li>
                        <li><a href="#">الشروط والأحكام</a></li>
                        <li><a href="#">سياسة الخصوصية</a></li>
                        <li><a href="#">المساعدة</a></li>
                        <li><a href="#">تواصل معنا</a></li>
                    </ul>
                </div>
                
                <div class="col-md-3 col-sm-6 mb-4">
                    <h4 class="footer-heading">النشرة الإخبارية</h4>
                    <p class="text-muted">اشترك في النشرة الإخبارية لتصلك أحدث العروض والخصومات</p>
                    <form class="mt-3">
                        <div class="input-group mb-3">
                            <input type="email" class="form-control" placeholder="البريد الإلكتروني">
                            <button class="btn btn-primary" type="button">اشتراك</button>
                        </div>
                    </form>
                    <div class="payment-methods mt-3">
                        <span class="me-2"><i class="fab fa-cc-visa fa-2x"></i></span>
                        <span class="me-2"><i class="fab fa-cc-mastercard fa-2x"></i></span>
                        <span class="me-2"><i class="fab fa-cc-paypal fa-2x"></i></span>
                        <span><i class="fab fa-cc-amex fa-2x"></i></span>
                    </div>
                </div>
            </div>
            
            <div class="footer-bottom">
                <p>&copy; <?php echo date('Y'); ?> جميع الحقوق محفوظة - موقع حجز الطيران</p>
            </div>
        </div>
    </footer>

    <!-- Bootstrap JavaScript -->
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>

    <!-- Custom JavaScript -->
    <script>
        // إخفاء طبقة Preloader بعد تحميل الصفحة
        window.addEventListener('load', function() {
            const preloader = document.querySelector('.preloader');
            preloader.classList.add('fade-out');
            
            setTimeout(function() {
                preloader.style.display = 'none';
            }, 500);
        });
        
        // تفعيل التلميحات (Tooltips)
        var tooltipTriggerList = [].slice.call(document.querySelectorAll('[data-bs-toggle="tooltip"]'))
        var tooltipList = tooltipTriggerList.map(function (tooltipTriggerEl) {
            return new bootstrap.Tooltip(tooltipTriggerEl)
        });
        
        // تفعيل النوافذ المنبثقة (Popovers)
        var popoverTriggerList = [].slice.call(document.querySelectorAll('[data-bs-toggle="popover"]'))
        var popoverList = popoverTriggerList.map(function (popoverTriggerEl) {
            return new bootstrap.Popover(popoverTriggerEl)
        });
        
        // دالة لتحديث عناصر تحديد التاريخ
        function updateDateInputs() {
            // تعيين الحد الأدنى لتاريخ المغادرة إلى اليوم الحالي
            const today = new Date().toISOString().split('T')[0];
            const departureDateInput = document.getElementById('departure_date');
            const returnDateInput = document.getElementById('return_date');
            
            if (departureDateInput) {
                departureDateInput.min = today;
                
                // التحقق من أن تاريخ العودة لا يسبق تاريخ المغادرة
                departureDateInput.addEventListener('change', function() {
                    if (returnDateInput) {
                        returnDateInput.min = this.value;
                        
                        if (returnDateInput.value && returnDateInput.value < this.value) {
                            returnDateInput.value = this.value;
                        }
                    }
                });
            }
        }
        
        // استدعاء دالة تحديث عناصر تحديد التاريخ عند تحميل الصفحة
        document.addEventListener('DOMContentLoaded', function() {
            updateDateInputs();
            
            // تفعيل/تعطيل حقل تاريخ العودة بناءً على نوع الرحلة
            const tripTypeRadios = document.querySelectorAll('input[name="trip_type"]');
            const returnDateInput = document.getElementById('return_date');
            
            if (tripTypeRadios.length > 0 && returnDateInput) {
                tripTypeRadios.forEach(function(radio) {
                    radio.addEventListener('change', function() {
                        if (this.value === 'round_trip') {
                            returnDateInput.removeAttribute('disabled');
                            returnDateInput.setAttribute('required', 'required');
                        } else {
                            returnDateInput.setAttribute('disabled', 'disabled');
                            returnDateInput.removeAttribute('required');
                        }
                    });
                });
            }
            
            // التحقق من عدم اختيار نفس المدينة للمغادرة والوصول
            const fromCitySelect = document.getElementById('from_city');
            const toCitySelect = document.getElementById('to_city');
            
            if (fromCitySelect && toCitySelect) {
                fromCitySelect.addEventListener('change', function() {
                    // إذا تم اختيار نفس المدينة في كلا القائمتين
                    if (this.value !== '' && this.value === toCitySelect.value) {
                        toCitySelect.value = ''; // إعادة تعيين قائمة المدينة الأخرى
                    }
                });
                
                toCitySelect.addEventListener('change', function() {
                    // إذا تم اختيار نفس المدينة في كلا القائمتين
                    if (this.value !== '' && this.value === fromCitySelect.value) {
                        fromCitySelect.value = ''; // إعادة تعيين قائمة المدينة الأخرى
                    }
                });
            }
            
            // التحقق من صحة تواريخ الرحلة
            const searchForm = document.querySelector('form[action*="search_flights"]');
            
            if (searchForm) {
                searchForm.addEventListener('submit', function(e) {
                    const departureDateInput = document.getElementById('departure_date');
                    const departureDateValue = new Date(departureDateInput.value);
                    const today = new Date();
                    today.setHours(0, 0, 0, 0);
                    
                    if (departureDateValue < today) {
                        e.preventDefault();
                        alert('تاريخ المغادرة لا يمكن أن يكون في الماضي');
                        return;
                    }
                    
                    // التحقق من تاريخ العودة إذا كان مطلوبًا
                    const returnDateInput = document.getElementById('return_date');
                    if (!returnDateInput.disabled) {
                        const returnDateValue = new Date(returnDateInput.value);
                        
                        if (returnDateValue < departureDateValue) {
                            e.preventDefault();
                            alert('تاريخ العودة يجب أن يكون بعد تاريخ المغادرة');
                            return;
                        }
                    }
                    
                    // التحقق من اختيار مدن مختلفة للمغادرة والوصول
                    if (fromCitySelect.value === toCitySelect.value) {
                        e.preventDefault();
                        alert('يجب اختيار مدن مختلفة للمغادرة والوصول');
                        return;
                    }
                });
            }
            
            // التحقق من اختيار مقعد في صفحة تسجيل الدخول للرحلة
            const checkInForm = document.getElementById('check_in_form');
            
            if (checkInForm) {
                checkInForm.addEventListener('submit', function(e) {
                    const seatSelected = document.querySelector('input[name="seat_number"]:checked');
                    
                    if (!seatSelected) {
                        e.preventDefault();
                        alert('يرجى اختيار مقعد قبل إكمال تسجيل الدخول.');
                    }
                });
            }
        });
    </script>
    
    <?php
    // إضافة سكريبتات خاصة بصفحة بطاقة الصعود
    if ($page === 'boarding_pass') {
        echo '
        <!-- إضافة مكتبة JsBarcode لإنشاء الباركود -->
        <script src="https://cdn.jsdelivr.net/npm/jsbarcode@3.11.5/dist/JsBarcode.all.min.js"></script>
        <!-- إضافة مكتبة qrcode.js لإنشاء رمز QR -->
        <script src="https://cdn.jsdelivr.net/npm/qrcode-generator@1.4.4/qrcode.min.js"></script>
        
        <script>
        document.addEventListener("DOMContentLoaded", function() {
            // إنشاء الباركود
            if (document.getElementById("barcode")) {
                JsBarcode("#barcode", "' . (isset($booking) ? $booking['booking_reference'] : 'SAMPLE') . '", {
                    format: "CODE128",
                    lineColor: "#000",
                    width: 2,
                    height: 50,
                    displayValue: false
                });
            }
            
            // إنشاء رمز QR
            if (document.getElementById("qrcode")) {
                var qr = qrcode(0, "M");
                qr.addData("' . (isset($booking) ? $booking['booking_reference'] : 'SAMPLE') . '");
                qr.make();
                
                document.getElementById("qrcode").innerHTML = qr.createImgTag(3);
            }
        });
        </script>
        ';
    }
    ?>
</body>
</html>
<?php
// إغلاق اتصال قاعدة البيانات
$conn->close();
?>
