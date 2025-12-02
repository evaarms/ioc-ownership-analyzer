import re
import ipaddress
from urllib.parse import urlparse, unquote

def deobfuscate_ioc(ioc: str) -> str:
    """Cleans common defensive obfuscations (hxxp, [.], etc)."""
    replacements = {
        r'hxxp': 'http',
        r'\[\.\]': '.',
        r'\[\:\]': ':',
        r'\bdot\b': '.'
    }
    clean_ioc = ioc
    for pattern, real in replacements.items():
        clean_ioc = re.sub(pattern, real, clean_ioc, flags=re.IGNORECASE)

    # Decode URL encoding if present
    if '%' in clean_ioc:
        clean_ioc = unquote(clean_ioc)

    return clean_ioc.strip().rstrip(".")

def validate_ipv4(ip: str) -> bool:
    try:
        ipaddress.IPv4Address(ip)
        return True
    except:
        return False

def validate_ipv6(ip: str) -> bool:
    try:
        ipaddress.IPv6Address(ip)
        return True
    except:
        return False

def is_ip(ioc: str) -> bool:
    """Checks if string is a valid IP, handling ports."""
    host = ioc
    if ":" in ioc and ioc.count(":") == 1 and "." in ioc: # IPv4 with port
        host = ioc.split(":")[0]
    elif ioc.startswith("[") and "]" in ioc: # IPv6 with brackets
        host = ioc[1:ioc.index("]")]

    return validate_ipv4(host) or validate_ipv6(host)

def is_domain(ioc: str) -> bool:
    """Checks for strict domain format."""
    if is_ip(ioc):
        return False
    if len(ioc) > 253:
        return False

    ioc = ioc.lower().rstrip(".")
    labels = ioc.split(".")

    # Basic rule: TLD cannot be numeric
    if labels[-1].isdigit():
        return False

    pattern = re.compile(r"^[a-z0-9-]{1,63}$")
    for lbl in labels:
        if not pattern.match(lbl):
            return False
        if lbl.startswith("-") or lbl.endswith("-"):
            return False
    return True

def is_url(ioc: str) -> bool:
    """Checks for URL format."""
    try:
        parsed = urlparse(ioc)
        if parsed.scheme not in ("http", "https") or not parsed.netloc:
            return False
        host = parsed.netloc.split(":")[0].rstrip(".") # Remove port
        return is_ip(host) or is_domain(host)
    except:
        return False

def classify_ioc(ioc: str):
    """Strict Order: IP -> URL -> Domain. Returns (clean_ioc, type)."""
    clean = deobfuscate_ioc(ioc)
    if is_ip(clean):
        ioc_type = "IP"
    elif is_url(clean):
        ioc_type = "URL"
    elif is_domain(clean):
        ioc_type = "Domain"
    else:
        ioc_type = "Unknown"
    return clean, ioc_type
