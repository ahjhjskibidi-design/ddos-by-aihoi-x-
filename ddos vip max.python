import requests
from collections import deque
from urllib.parse import urljoin
import time

class URLQueue:
    def __init__(self, base_url, max_depth):
        self.base_url = base_url
        self.max_depth = max_depth
        self.queue = deque()
        self.visited = set()

    def add(self, path, depth=0):
        full_url = urljoin(self.base_url, path)
        if full_url not in self.visited and depth <= self.max_depth:
            self.queue.append((full_url, depth))

    def get_next(self):
        if self.queue:
            return self.queue.popleft()
        return None, None

    def is_empty(self):
        return len(self.queue) == 0

    def mark_visited(self, url):
        self.visited.add(url)


class HTTPFetcher:
    def __init__(self, timeout=5, retries=2):
        self.timeout = timeout
        self.retries = retries
        self.session = requests.Session()
        self.session.headers.update({
            "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36"
        })

    def fetch(self, url):
        for attempt in range(self.retries + 1):
            try:
                response = self.session.get(url, timeout=self.timeout)
                print(f"[{response.status_code}] {url}")
                return response.status_code, response.text[:100]
            except:
                time.sleep(1)
        return None, None


if __name__ == "__main__":
    BASE = input("Nhap URL target (vd: http://example.com): ")
    WORDLIST = ["admin", "login", "api", "test", "backup", "config", "wp-admin", "user", "upload", "images"]

    queue = URLQueue(BASE, 2)
    fetcher = HTTPFetcher(timeout=5, retries=2)

    queue.add("/", 0)

    while not queue.is_empty():
        url, depth = queue.get_next()
        if not url:
            break

        status, _ = fetcher.fetch(url)
        queue.mark_visited(url)

        if status and status in [200, 403]:
            for word in WORDLIST:
                new_path = url.rstrip("/") + "/" + word
                queue.add(new_path, depth + 1)