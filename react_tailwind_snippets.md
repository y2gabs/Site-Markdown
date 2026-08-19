# Golden React Snippets & Tailwind Configurations

Use these exact code snippets when generating standalone React/Babel HTML files to ensure bug-free execution and contemporary animations without relying on external animation libraries.

## 1. Native Scroll Hook (Performance Optimized)
Always use this hook to track scroll position for parallax effects. Do not use external libraries.

```javascript
const useScrollY = () => {
    const [scrollY, setScrollY] = useState(0);
    useEffect(() => {
        const handleScroll = () => {
            window.requestAnimationFrame(() => setScrollY(window.scrollY));
        };
        window.addEventListener('scroll', handleScroll, { passive: true });
        return () => window.removeEventListener('scroll', handleScroll);
    }, []);
    return scrollY;
};