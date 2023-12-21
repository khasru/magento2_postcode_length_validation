# Magento2 postcode / zipcode length validation and remove waring complete guide

In that case we need to work on multiple files.

Firstly we need to add **requirejs-config.js** file into your app/code/**`Vendor/Module`**/view/frontend/requirejs-config.js

    var config = {
        config: {
            mixins: {
                'Magento_Ui/js/lib/validation/rules': {
                    'Vendor_Module/js/lib/validation/rules': true
                }
            }
        }
    };


And then add your custom rules.js file into **/app/code/Vendor/Module/view/frontend/web/js/lib/validation/rules.js**


    define([
        'jquery',
        'underscore',
        'moment',
        'Magento_Ui/js/lib/validation/utils',
        'mage/translate'
    ], function ($, _, moment,utils) {
        'use strict';
    
        return function (validator) {
            var validators = {
                'validate-zip-ch': [
                    function (value) {
                       var country_id= jQuery('select[name="country_id"]').val();
                       if(country_id=="CH"){
                           return utils.isEmptyNoTrim(value) || /(^\d{4}$)|(^\d{4}-\d{4}$)/.test(value);
                       }else if(country_id=="DE"){
                         //  return utils.isEmptyNoTrim(value) || /(^\d{5}$)|(^\d{5}-\d{5}$)/.test(value);
                           return true;
                       }else{
                           return true;
                       }
                    },
                    $.mage.__('The postal code appears to be incorrect. Please enter a four-digit Swiss postcode.')
                ],
            };
    
            validators = _.mapObject(validators, function (data) {
                return {
                    handler: data[0],
                    message: data[1]
                };
            });
    
            return $.extend(validator, validators);
        };
    });



Important thing is postcode length is very country to country, here I handle this conditions, and also added custom message which I will manipulate language by csv files

**$.mage.__('The postal code appears to be incorrect. Please enter a four-digit Swiss postcode.')** 


Finally I added this into the checkout_index_index.xml 

    <item name="postcode" xsi:type="array">
        <item name="sortOrder" xsi:type="string">70</item>
        <item name="validation" xsi:type="array">
            <item name="validate-zip-ch" xsi:type="string">true</item>
        </item>
    </item>


Now I  remove warning message, for this I also add the zip_codes.xml to the 
**/app/code/Vendor/Module/etc/zip_codes.xml**


    <?xml version="1.0"?>
    <!--
    /**
     * Copyright © Magento, Inc. All rights reserved.
     * See COPYING.txt for license details.
     */
    -->
    <config xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="urn:magento:module:Magento_Directory:etc/zip_codes.xsd">
    
        <zip countryCode="CH">
            <codes>
                <code id="pattern_1" active="false" example="1234">^[0-9]{4}$</code>
            </codes>
        </zip>
    
    </config>

Finally i added to translation into de_CH.csv file.

    "The postal code appears to be incorrect. Please enter a four-digit Swiss postcode.","Die Postleitzahl scheint falsch zu sein. Bitte geben Sie eine vierstellige Schweizer Postleitzahl ein."


[![enter image description here][1]][1]


  [1]: https://i.stack.imgur.com/Oqac4.png


  **stackexchange**: https://magento.stackexchange.com/posts/371372/edit 
