<html lang="ur">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>12th Fail فاسٹ فوڈ آرڈر</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        /* Custom styles not easily done with Tailwind */
        body {
            direction: rtl; /* Right-to-left for Urdu */
            text-align: right; /* Align text to the right */
            font-family: 'Arial', sans-serif; /* Use a common font */
        }
        /* Style for the print token window */
        @media print {
            body {
                direction: rtl;
                text-align: right;
                font-family: 'Arial', sans-serif;
                padding: 10mm; /* Add some padding for printing */
            }
            .token-container {
                border: 1px dashed #000; /* Dashed border for token */
                padding: 15px;
                max-width: 300px; /* Limit token width */
                margin: 0 auto; /* Center token on print */
            }
            .token-header h1 {
                color: #d32f2f;
                text-align: center;
                margin-bottom: 10px;
                font-size: 1.5em;
            }
            .token-details p {
                margin-bottom: 5px;
                font-size: 0.9em;
            }
            .token-items ul {
                list-style: none;
                padding: 0;
                margin-bottom: 10px;
            }
            .token-items li {
                margin-bottom: 3px;
                font-size: 0.9em;
                 /* Style for item name, quantity, and price */
                display: flex;
                justify-content: space-between;
            }
            .token-total {
                font-weight: bold;
                font-size: 1em;
                margin-top: 10px;
                border-top: 1px dashed #ccc;
                padding-top: 5px;
            }
            .token-footer {
                text-align: center;
                margin-top: 15px;
                font-style: italic;
                font-size: 0.8em;
            }
        }
    </style>
</head>
<body class="bg-gray-100 p-6">

    <div class="container mx-auto max-w-md bg-white rounded-lg shadow-lg p-6">
        <h1 class="text-3xl font-bold text-red-700 text-center mb-6">فاسٹ فوڈ آڈر بکنگ</h1>

        <div class="mb-4">
            <label for="customerName" class="block text-gray-700 font-bold mb-2">کسٹمر کا نام:</label>
            <input type="text" id="customerName" class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline">
        </div>

        <div class="mb-6">
            <label for="contactNumber" class="block text-gray-700 font-bold mb-2">کنٹیکٹ نمبر:</label>
            <input type="text" id="contactNumber" class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline">
        </div>

        <h2 class="text-2xl font-semibold text-red-600 mb-4">آئٹم منتخب کریں:</h2>
        <div id="menu" class="space-y-3">
            </div>

        <div id="liveSummary" class="mt-6 p-4 bg-blue-100 border border-blue-300 rounded-md hidden">
            <h3 class="text-xl font-semibold text-blue-800 mb-3 border-b border-blue-300 pb-2">آپ کا آڈر</h3>
            <ul id="summaryItems" class="list-none p-0 m-0">
                </ul>
            <p id="summaryTotal" class="font-bold text-blue-900 text-lg mt-3"></p>
        </div>


        <button onclick="placeOrder()" class="w-full bg-red-600 hover:bg-red-700 text-white font-bold py-3 px-4 rounded-md focus:outline-none focus:shadow-outline transition duration-200 ease-in-out mt-6">
            آڈر کریں
        </button>

        <div id="orderConfirmation" class="mt-6 p-4 bg-green-100 border border-green-300 rounded-md text-center font-bold text-green-800 hidden">
            </div>
    </div>


    <script>
        // فرضی مینو آئٹمز
        const items = [
            { name: 'چکن برگر', price: 200 },
            { name: 'بیف برگر', price: 500 },
            { name: 'فرائز', price: 300 },
            { name: 'زنگر برگر', price: 350 },
            { name: 'چکن رول', price: 150 },
            { name: 'پیپسی', price: 200 },
            { name: 'چکن پیٹا', price: 600 },
            { name: 'بروسٹ', price: 700 }
        ];

        // مینو شو کریں
        const menuDiv = document.getElementById('menu');
        items.forEach((item, index) => {
            menuDiv.innerHTML += `
                <div class="menu-item flex justify-between items-center bg-yellow-100 border border-yellow-300 rounded-md p-3 shadow-sm">
                    <div class="item-info flex-grow ml-4">
                         <span class="font-semibold text-gray-800">${item.name}</span> - <span class="text-gray-600">Rs.${item.price}</span>
                    </div>
                    <div class="quantity-control flex items-center">
                        <button onclick="changeQuantity(${index}, -1)" class="bg-yellow-400 hover:bg-yellow-500 text-gray-800 font-bold py-1 px-3 rounded-l-md focus:outline-none focus:shadow-outline">-</button>
                        <input type="number" id="item-${index}" min="0" value="0" oninput="updateSummary()" class="w-12 text-center border-t border-b border-yellow-300 py-1 px-0 text-gray-700 leading-tight focus:outline-none focus:shadow-outline [-moz-appearance:_textfield] [&::-webkit-outer-spin-button]:m-0 [&::-webkit-inner-spin-button]:m-0">
                        <button onclick="changeQuantity(${index}, 1)" class="bg-yellow-400 hover:bg-yellow-500 text-gray-800 font-bold py-1 px-3 rounded-r-md focus:outline-none focus:shadow-outline">+</button>
                    </div>
                </div>
            `;
        });

        // Function to change quantity using buttons
        function changeQuantity(index, delta) {
            const quantityInput = document.getElementById(`item-${index}`);
            let currentValue = parseInt(quantityInput.value);
            if (isNaN(currentValue)) currentValue = 0;
            const newValue = Math.max(0, currentValue + delta); // Ensure quantity is not negative
            quantityInput.value = newValue;
            updateSummary(); // Update summary after changing quantity
        }

        // Function to update the live order summary
        function updateSummary() {
            let orderedItems = [];
            let total = 0;

            items.forEach((item, index) => {
                const quantity = parseInt(document.getElementById(`item-${index}`).value);
                 if (isNaN(quantity)) quantity = 0; // Handle non-numeric input
                if (quantity > 0) {
                    // Store item details including calculated price for summary
                    orderedItems.push({
                        name: item.name,
                        quantity: quantity,
                        price: item.price,
                        subtotal: item.price * quantity
                    });
                    total += item.price * quantity;
                }
            });

            const summaryDiv = document.getElementById('liveSummary');
            const summaryItemsUl = document.getElementById('summaryItems');
            const summaryTotalP = document.getElementById('summaryTotal');

            if (orderedItems.length > 0) {
                summaryDiv.classList.remove('hidden'); // Show summary if items are selected
                // Display item name, quantity, and subtotal in the live summary
                summaryItemsUl.innerHTML = orderedItems.map(item => `<li>${item.name} x ${item.quantity} - Rs.${item.subtotal}</li>`).join('');
                summaryTotalP.innerText = `کل رقم: Rs.${total}`;
            } else {
                summaryDiv.classList.add('hidden'); // Hide summary if no items are selected
                summaryItemsUl.innerHTML = '';
                summaryTotalP.innerText = '';
            }
        }

        // Call updateSummary initially to set display state
        updateSummary();


        let tokenNumber = 1; // Start token number

        // Google Form URL (اپنا فارم لنک اور فیلڈ IDs یہاں لگائیں)
        // IMPORTANT: Replace with your actual Google Form pre-filled URLs for each field
        const GOOGLE_FORM_ACTION_URL = 'https://docs.google.com/forms/d/e/your-form-id/formResponse'; // <-- Replace 'your-form-id'
        const FIELD_CUSTOMER_NAME = 'entry.1234567890'; // <-- Replace with actual entry ID
        const FIELD_CONTACT_NUMBER = 'entry.2345678901'; // <-- Replace with actual entry ID
        const FIELD_ITEMS = 'entry.3456789012'; // <-- Replace with actual entry ID
        const FIELD_TOTAL = 'entry.4567890123'; // <-- Replace with actual entry ID
        const FIELD_TOKEN = 'entry.5678901234'; // <-- Replace with actual entry ID

        function placeOrder() {
            const customerNameInput = document.getElementById('customerName');
            const contactNumberInput = document.getElementById('contactNumber');

            const customerName = customerNameInput.value.trim();
            const contactNumber = contactNumberInput.value.trim();

            if (!customerName || !contactNumber) {
                alert('براہ کرم کسٹمر کا نام اور نمبر درج کریں');
                return;
            }

            let orderedItemsForToken = []; // Array to build the list for the token
            let total = 0;

            items.forEach((item, index) => {
                const quantity = parseInt(document.getElementById(`item-${index}`).value);
                 if (isNaN(quantity)) quantity = 0; // Handle non-numeric input
                if (quantity > 0) {
                    const itemSubtotal = item.price * quantity;
                    orderedItemsForToken.push({ // Store details for token display
                        name: item.name,
                        quantity: quantity,
                        subtotal: itemSubtotal
                    });
                    total += itemSubtotal;
                }
            });

            if (orderedItemsForToken.length === 0) {
                alert('براہ کرم کم از کم ایک آئٹم منتخب کریں');
                return;
            }

            // ٹوکن تیار کریں
            const tokenHTML = `
                <!DOCTYPE html>
                <html lang="ur">
                <head>
                    <meta charset="UTF-8">
                    <meta name="viewport" content="width=device-width, initial-scale=1.0">
                    <title>ٹوکن</title>
                     <style>
                        /* Styles for the print token */
                        body {
                            font-family: 'Arial', sans-serif;
                            padding: 20px;
                            direction: rtl;
                            text-align: right;
                            margin: 0; /* Remove default body margin */
                            background-color: #fff; /* White background for printing */
                        }
                        .token-container {
                            border: 2px dashed #000; /* Clearer dashed border */
                            padding: 20px;
                            max-width: 350px; /* Slightly wider token */
                            margin: 20px auto; /* Center the token */
                            box-shadow: 0 0 10px rgba(0,0,0,0.1); /* Subtle shadow */
                        }
                        .token-header {
                            border-bottom: 2px solid #d32f2f; /* Red border below header */
                            padding-bottom: 10px;
                            margin-bottom: 15px;
                            text-align: center;
                        }
                        .token-header h1 {
                            color: #d32f2f;
                            margin: 0;
                            font-size: 1.8em; /* Larger header */
                            font-weight: bold;
                        }
                        .token-details p {
                            margin-bottom: 8px; /* More space between details */
                            font-size: 1em;
                            line-height: 1.4;
                        }
                         .token-details p strong {
                             color: #555;
                         }
                        .token-items {
                            margin-top: 15px;
                            border-top: 1px solid #eee; /* Light border above items */
                            padding-top: 10px;
                        }
                        .token-items h2 {
                            font-size: 1.2em;
                            color: #333;
                            margin-top: 0;
                            margin-bottom: 10px;
                             border-bottom: 1px dashed #ccc;
                             padding-bottom: 5px;
                        }
                        .token-items ul {
                            list-style: none;
                            padding: 0;
                            margin: 0;
                        }
                        .token-items li {
                            margin-bottom: 6px;
                            font-size: 1em;
                            color: #444;
                            display: flex; /* Use flexbox for alignment */
                            justify-content: space-between; /* Space out item info and price */
                        }
                         .item-info-token {
                             flex-grow: 1; /* Allow item info to take space */
                             margin-left: 10px; /* Space between info and price */
                         }
                         .item-price-token {
                             font-weight: bold;
                         }
                        .token-total {
                            font-weight: bold;
                            font-size: 1.2em; /* Larger total */
                            margin-top: 15px;
                            border-top: 2px solid #d32f2f; /* Red border above total */
                            padding-top: 10px;
                            text-align: center;
                            color: #d32f2f; /* Red total text */
                        }
                        .token-footer {
                            text-align: center;
                            margin-top: 20px;
                            font-style: italic;
                            font-size: 0.9em;
                            color: #666;
                        }
                         @media print {
                            /* Ensure styles apply correctly when printing */
                            body {
                                background-color: #fff !important;
                                -webkit-print-color-adjust: exact; /* For better color printing */
                                color-adjust: exact;
                            }
                             .token-container {
                                 border: 2px dashed #000 !important;
                                 box-shadow: none !important;
                             }
                             .token-header h1, .token-total {
                                 color: #d32f2f !important;
                             }
                             .token-header, .token-total {
                                 border-color: #d32f2f !important;
                             }
                             .token-items h2 {
                                 color: #333 !important;
                             }
                             .token-items li {
                                 color: #444 !important;
                             }
                             .token-details p strong {
                                 color: #555 !important;
                             }
                             .token-footer {
                                 color: #666 !important;
                             }

                        }
                     </style>
                </head>
                <body>
                    <div class="token-container">
                        <div class="token-header">
                            <h1>فاسٹ فوڈ ٹوکن</h1>
                        </div>
                        <div class="token-details">
                            <p><strong>ٹوکن نمبر:</strong> ${tokenNumber}</p>
                            <p><strong>کسٹمر کا نام:</strong> ${customerName}</p>
                            <p><strong>کنٹیکٹ نمبر:</strong> ${contactNumber}</p>
                        </div>
                        <div class="token-items">
                             <h2>آئٹمز:</h2>
                            <ul>
                                ${orderedItemsForToken.map(item => `
                                    <li>
                                        <span class="item-info-token">${item.name} x ${item.quantity}</span>
                                        <span class="item-price-token">Rs.${item.subtotal}</span>
                                    </li>
                                `).join('')}
                            </ul>
                        </div>
                        <p class="token-total"><strong>کل رقم:</strong> Rs.${total}</p>
                        <p class="token-footer">ویٹنگ ٹائم: 30 منٹ</p>
                        <p class="token-footer">--- "12th Fail" فاسٹ فوڈ ریسٹورنٹ میں آرڈر کرنے کا شکریہ ---</p>
                        <p class="token-footer">"یہ ویب سائٹ صرف ڈیمو پر چیک کرنے کے لیے بنائی گئی ہے رابطہ کریں 03168910150"</p> </div>
                    <script>
                        // Automatically print when window loads
                        window.onload = function() {
                            window.print();
                            // Close window after printing (optional, might not work in all browsers)
                            // window.onafterprint = function() {
                            //     window.close();
                            // }
                        }
                    <\/script>
                </body>
                </html>
            `;

            // ٹوکن نئی ونڈو میں کھولیں اور پرنٹ کریں
            const printWindow = window.open('', '_blank', 'height=600,width=400'); // Open in new blank tab
            printWindow.document.write(tokenHTML);
            printWindow.document.close(); // Close the document stream


            // ڈیٹا گوگل فارم میں بھیجیں
            // نوٹ: یہ ڈیٹا بھیجنے کی کوشش کرے گا، لیکن آپ کو Google Form URL اور فیلڈ IDs کو اپنی اصل تفصیلات سے تبدیل کرنا ضروری ہے۔
            // 'no-cors' موڈ میں، آپ کو سبمیشن کی کامیابی کے بارے میں تفصیلی معلومات نہیں مل سکیں گی۔
            const formData = new FormData();
            formData.append(FIELD_CUSTOMER_NAME, customerName);
            formData.append(FIELD_CONTACT_NUMBER, contactNumber);
            // For Google Form, sending a simple string representation of items with prices
            const itemsForGoogleForm = orderedItemsForToken.map(item => `${item.name} x ${item.quantity} (Rs.${item.subtotal})`).join(', ');
            formData.append(FIELD_ITEMS, itemsForGoogleForm);
            formData.append(FIELD_TOTAL, total);
            formData.append(FIELD_TOKEN, tokenNumber);

            fetch(GOOGLE_FORM_ACTION_URL, {
                method: 'POST',
                mode: 'no-cors', // Stay with no-cors as we don't control Google Forms directly
                body: formData
            }).then(() => {
                console.log('Data submission attempt to Google Form');
                // In no-cors mode, this fetch will resolve even if the server responded with an error.
                // Actual success cannot be confirmed client-side easily with this method.
            }).catch((error) => {
                console.error('Error during Google Form submission attempt:', error.message);
                 // This catch block might only catch network errors, not server-side form processing errors.
            });


            // خلاصہ شو کریں
            const confirmationDiv = document.getElementById('orderConfirmation');
            confirmationDiv.innerHTML = `آڈر کامیابی سے ہو گیا۔ ٹوکن نمبر: <strong>${tokenNumber}</strong>`;
            confirmationDiv.classList.remove('hidden'); // Show confirmation message


            tokenNumber++; // Increment token number for the next order

            // فارم کلیئر کریں
            customerNameInput.value = '';
            contactNumberInput.value = '';
            items.forEach((item, index) => {
                document.getElementById(`item-${index}`).value = '0';
            });
            updateSummary(); // Hide summary and clear lists after order
        }
    </script>

</body>
</html>

