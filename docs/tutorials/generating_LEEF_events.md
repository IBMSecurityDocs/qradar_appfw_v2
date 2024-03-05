# LEEF events 

The Log Event Extended Format (LEEF) is a customized event format for IBM QRadar. You can follow this procedure to generate LEEF events in your app.

You can send events in LEEF output to QRadar by using the following protocols:

- Syslog
- File import with the Log File Protocol

In this tutorial, use a syslog header since apps will send events to QRadar via syslog.

## Prerequisites

- QRadar App SDK v2
- [Python](https://www.python.org/downloads/) 3.8 or later

## Considerations

The LEEF Specification document is generally for Security Intelligence Partner Program members. If you're unsure about your permissions, please contact IBM Support. 

## Syslog header

The syslog header contains the timestamp and IPv4 address or host name of the system that provides the event. The syslog header is an optional component of the LEEF format, because it only serves a purpose if the events are sent to QRadar via syslog. If you include a syslog header, you must separate the syslog header from the LEEF header with a space. The syslog header must conform to the formats specified in RFC 3164 or RFC 5424.

`<_priority tag_><_timestamp_> <_IP address or hostname_>`

The priority tag is sometimes optional, depending on how the header is formatted. Handling of RFC3164 headers does not require the priority tag, but handling of RFC5424 headers does require the priority tag. 

**Tip:** Use the RFC5424 format, rather than RFC3164, because the RFC5424 timestamp includes the year and time zone.

Examples of RFC5424 headers:

- `<13>1 2019-01-18T11:07:53.520Z 192.168.1.1`
- `<133>1 2019-01-18T11:07:53.520+07:00 _myhostname_`

## LEEF header

The LEEF header is a required field for LEEF events. The LEEF header is a pipe delimited (|) set of values that identifies your software or appliance to QRadar. 

**Examples:**

- `LEEF:Version|Vendor|Product|Version|EventID|`
- `LEEF:1.0|Microsoft|MSExchange|4.0 SP1|15345| `
- `LEEF:2.0|Lancope|StealthWatch|1.0|41|^|`

The attributes are tab separated in LEEF 1.0. In LEEF 2.0, the delimiter for the attributes can be specified in the LEEF header. 

Example of a LEEF 1.0 Header:

`LEEF:1.0|Vendor|Product|Version|EventID|Key1=Value1<tab>Key2=Value2<tab>Key3=Value3<tab>...<tab>KeyN=ValueN`

Use the DelimiterCharacter in the LEEF 2.0 header to specify an alternate delimiter to the attributes. You can use a single character or the hex value for that character. The hex value can be represented by the prefix `0x` or `x`, followed by a series of 1-4 characters (`0-9A-Fa-f`).

| Delimiter                 | Header                                             |
|---------------------------|----------------------------------------------------|
| Caret ( ^ )               | `LEEF:2.0\|Vendor\|Product\|Version\|EventID\|^\|`   |
| Caret (hex value)         | `LEEF:2.0\|Vendor\|Product\|Version\|EventID\|x5E\|` |
| Broken vertical bar ( ¦ ) | `LEEF:2.0\|Vendor\|Product\|Version\|EventID\|xa6\|` |

### LEEF version

The LEEF version information is an integer value that identifies the major and minor version of the LEEF format that is used for the event.

Example:

`LEEF:1.0|Vendor|Product|Version|EventID|`

### Vendor

**Vendor** is a text string that identifies the vendor or manufacturer of the device that sends the syslog events in LEEF format. The Vendor and Product field must contain unique values when specified in the LEEF header. 

**Example:** `LEEF:1.0|Microsoft|Product|Version|EventID|`

In this example, the company name (Microsoft) is the value assigned to the vendor attribute.

### Product name

The **Product** field is a text string that identifies the product that sends the event log to QRadar. 

**Example:** `LEEF:1.0|Microsoft|MSExchange|Version|EventID|`

**Tip:** Use app name if the events that are generated describe activity within the app itself. But many apps that generate events are actually calling the APIs of external products and relaying the event data retrieved by those API calls into syslog messages. If the app is one of those intermediates that is a mechanism for collecting events from an external product, set the Product to the name of the product whose events are being collected. 

### Product version 

**Version** is a string that identifies the version of the software or appliance that sends the event log.

**Example:** `LEEF:1.0|Microsoft|MSExchange|4.0 SP1|EventID|`

The product version should align with either the version of the app or the version of the external product the app is communicating with, to obtain events.
The Product field must contain unique values when specified in the LEEF header. 

### EventID

**EventID** is a unique identifier for an event in the LEEF header. An **EventID** can contain either a numeric identified or a text description.

**Examples:**

- `LEEF:1.0|Microsoft|MSExchange|2007|7732|`
- `LEEF:1.0|Microsoft|MSExchange|2007|Logon Failure|`

## Event attributes

Event attributes identify the payload information of the event that your appliance or software produces. Every event attribute is a key and value pair with a tab that separates individual payload events. The LEEF format contains a number of predefined event attributes that allow QRadar to categorize and display the event.

The order of the event attributes is not enforced. 
Use the pre-defined set of keys when possible. 
Keys cannot contain spaces or equal signs (=), and values cannot contain tabs. 

## Generating a LEEF header

You can construct a LEEF header by using the following Python code. Use the `LEEF_TEMPLATE` to format the header's attributes. This example includes two functions, one generates QRadar specific LEEF headers, and the other generates custom LEEF headers. 

```python
#!/usr/bin/env python3

class LeefEventGenerator():

    LEEF_TEMPLATE = 'LEEF:1.0|VENDOR_NAME={0}|PRODUCT_NAME={1}|PRODUCT_VERSION={2}|QRadarAppLog|APP_ID={3} LOG_FILE={4} LOG_MESSAGE={5}'

    APP_ID = os.getenv('QRADAR_APP_ID', '0')
    VENDOR__NAME = 'QRadar'
    PRODUCT_NAME = 'QRadarAppLogger'
    PRODUCT_VERSION = '1.0'

    def createQRadarLeefHeader(self, message, log_file):
        # This function generates a QRadar specific LEEF header and will automatically set the 
        # APP_ID, VENDOR_NAME, PRODUCT_NAME and PRODUCT_VERSION values
        return LEEF_TEMPLATE.format(self.VENDOR__NAME, self.PRODUCT_NAME, self.PRODUCT_VERSION, self.APP_ID, log_file, message)

    def createCustomLeefHeader( self, vendor, product_name, product_version, app_id, log_file, message):
        # This function generates a custom LEEF header where custom values can be input
        return LEEF_TEMPLATE.format(vendor, product_name, product_version, app_id, log_file, message)
        
```

## Custom attributes 

In some cases, custom attributes are required to identify more information about the generated event. In these cases, you can define your own custom attributes and include them in the event log. 

**Tip:** Use custom attribute fields only when there is no acceptable mapping to a predefined field. You can use them for viewing in the QRadar Event Viewer by creating custom properties and by the QRadar reporting engine by creating customer properties. You cannot use custom attribute fields for event correlation. 

Custom attributes keys must follow these rules:

- Single word no spaces
- Alphanumeric
- Clear and concise
- Cannot be named the same as any predefined attribute key

## Date formats

The date format for the `devTime` field is specified using the `devTimeFormat` key.

Examples:

- `Jun 06 2010 16:07:36  -> devTimeFormat=MMM dd yyyy HH:mm:ss`
- `Jun 06 2010 16:07:36.300 -> devTimeFormat=MMM dd yyyy HH:mm:ss.SSS`

For more information about specifying date formats, see [Information on specifying a date format](http://java.sun.com/javase/6/docs/api/java/text/SimpleDateFormat.html).

## Additional information 

[IBM QRadar Log Event Extended Format (LEEF) Version 2 Doc](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/pdf/b_Leef_format_guide.pdf)
