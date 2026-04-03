VOID RECON
Advanced Web Security Toolkit
==============================

A fast, multi-threaded web security toolkit for authorized penetration testing.
Covers directory brute-forcing, URL pattern fuzzing, bulk status checking,
and 403 Forbidden bypass with baseline comparison filtering.

For legal use only. Run only against systems you own or have written permission to test.


INSTALLATION
------------

    git clone https://github.com/alone-beast/void-recon
    cd void-recon
    chmod +x setup.sh
    sudo ./setup.sh

After setup, the tool is available globally as:

    void-recon


USAGE
-----

    void-recon -m <module> [options]

Modules:

    fuzz        Directory brute-force with a wordlist
    pattern     Replace FUZZ keyword in URL with wordlist entries
    check       Bulk HTTP status checker across a URL list
    bypass      403 Forbidden bypass via path manipulation and header injection


OPTIONS
-------

    -u  --url        Target URL
    -w  --wordlist   Path to wordlist or URL list file
    -p  --payloads   Custom payload file (bypass module, custom mode)
    -t  --threads    Thread count                        (default: 30)
    -T  --timeout    Request timeout in seconds          (default: 10)
    -x  --ext        File extensions to append           (e.g. .php,.html)
    -fc --filter     Show only these status codes        (e.g. 200,301)
    -o  --output     Save results to file
    -h  --help       Show help and usage


EXAMPLES
--------

Directory fuzzing, show only 200 and 301:

    void-recon -m fuzz -u https://example.com -w wordlist.txt -fc 200,301 -o results.txt

FUZZ keyword pattern scan:

    void-recon -m pattern -u https://example.com/api/v1/FUZZ -w params.txt -fc 200

Bulk status check, filter 200 and 403 only:

    void-recon -m check -w urls.txt -fc 200,403 -o alive.txt

403 bypass, auto mode (path manipulation + header injection):

    void-recon -m bypass -u https://example.com/admin -o bypass.txt

403 bypass, custom payload file:

    void-recon -m bypass -u https://example.com/admin -p payloads.txt -o bypass.txt


403 BYPASS - HOW IT WORKS
--------------------------

Payloads are applied relative to the full original path, not just the domain.

Given target: https://example.com/status/403

    PATH-AFTER   https://example.com/status/403/%20
    PATH-BEFORE  https://example.com/%2f/status/403
    MID-PATH     https://example.com/status/./403
    HEADER-ONLY  Original URL + X-Forwarded-For, X-Real-IP, etc.
    COMBINED     Path variant + header injection together

Baseline Comparison:
    The tool fetches the 403 page first and records its Content-Length.
    Any bypass response with a size within 50 bytes of the baseline is
    suppressed as a false positive, regardless of status code.


REQUIREMENTS
------------

    Python 3.7+
    requests
    colorama
    urllib3


DISCLAIMER
----------

This tool is intended for authorized security testing only.
The author is not responsible for misuse or damage caused by this tool.
Always obtain written permission before testing any system.
