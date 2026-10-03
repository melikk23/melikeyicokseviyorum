import turtle as t, math as m, random as r

s = t.Screen()
s.setup(700, 700)
s.bgcolor("black")
cv = s.getcanvas()   # turtle yerine doğrudan canvas: çok daha hızlı

# Koordinatlar turtle ile aynı: (0,0) merkez, y yukarı
# (canvas'a verirken y işareti çevriliyor)


def heart(a, scale):
    x = 16 * (m.sin(a) ** 3) * scale
    y = (13 * m.cos(a) - 5 * m.cos(2 * a) - 2 * m.cos(3 * a) - m.cos(4 * a)) * scale
    return x, y


def hexc(rr, gg, bb):
    return "#{:02x}{:02x}{:02x}".format(int(rr), int(gg), int(bb))


def pts_heart(cx, cy, half):
    """Merkezi (cx, cy), yarı genişliği 'half' olan kalbin canvas noktaları."""
    sc = half / 16
    out = []
    for deg in range(0, 360, 10):
        x, y = heart(m.radians(deg), sc)
        out += [cx + x, -(cy + y)]
    return out


# ---------- 1. katman: kalbin içini dolduran ışınlar ----------
N1, N2 = 3500, 2500


def katman1(i=0):
    for _ in range(160):
        if i >= N1:
            break
        a = r.uniform(0, 2 * m.pi)
        x, y = heart(a, r.uniform(0.5, 15.5))
        ang = m.atan2(y, x) + r.uniform(-0.5, 0.5)
        ln = r.uniform(4, 14)
        col = hexc(255, 255 * r.uniform(0.25, 0.55), 255 * r.uniform(0.65, 0.85))
        cv.create_line(x, -y, x + ln * m.cos(ang), -(y + ln * m.sin(ang)),
                       fill=col, width=r.uniform(0.8, 1.6))
        i += 1
    if i < N1:
        s.ontimer(lambda: katman1(i), 10)
    else:
        katman2(0)


# ---------- 2. katman: kenardaki parlak ışınlar ----------
def katman2(i=0):
    for _ in range(120):
        if i >= N2:
            break
        a = r.uniform(0, 2 * m.pi)
        x, y = heart(a, 16.0)
        x += r.uniform(-2, 2)
        y += r.uniform(-2, 2)
        ang = m.atan2(y, x) + r.uniform(-0.35, 0.35)
        ln = r.uniform(10, 32)
        col = hexc(255, 255 * r.uniform(0.45, 0.75), 255 * r.uniform(0.75, 0.95))
        cv.create_line(x, -y, x + ln * m.cos(ang), -(y + ln * m.sin(ang)),
                       fill=col, width=r.uniform(0.7, 1.4))
        i += 1
    if i < N2:
        s.ontimer(lambda: katman2(i), 10)
    else:
        final()


# ---------- Final: kalpler, yazı, atan kalp ----------
PEMBELER = ["#ff69b4", "#ff3c78", "#ff96c8", "#ff5a8c", "#ffa6c9", "#ff7eb3"]
hearts = []
beat = None
metin = []        # (öğe, satır no)
outline_items = []

FONT1 = ("Segoe Script", 40, "bold")   # olmazsa "Georgia" dene
FONT2 = ("Segoe Script", 20, "bold")
PEMBE1 = (255, 95, 162)      # "Melike"
PEMBE2 = (255, 179, 209)     # "benim her şeyim"


def final():
    global beat
    # Kalbin parlak kenar çizgisi
    cv.create_polygon(pts_heart(0, 0, 256), fill="", outline="#ffc2dc",
                      width=2, smooth=True)

    # Süzülen küçük kalpler
    for _ in range(14):
        size = r.randint(7, 16)
        h = {"x": r.randint(-300, 300), "y": r.randint(-330, 330),
             "v": r.uniform(0.8, 2.0), "s": size, "ph": r.uniform(0, 6.28)}
        h["id"] = cv.create_polygon(pts_heart(h["x"], h["y"], size),
                                    fill=r.choice(PEMBELER), outline="", smooth=True)
        hearts.append(h)

    # Yazının altında atan kalp
    beat = cv.create_polygon(pts_heart(0, -105, 13), fill="#ff2d6f",
                             outline="#ffd0e2", smooth=True)

    # Yazılar: önce siyah kontur (gizli), sonra pembe yazı
    satirlar = [("Melike", 5, FONT1), ("benim her şeyim", 55, FONT2)]
    for no, (txt, cy, font) in enumerate(satirlar):
        outs = []
        for dx, dy in ((-3, 0), (3, 0), (0, -3), (0, 3), (-2, -2), (2, -2), (-2, 2), (2, 2)):
            o = cv.create_text(dx, cy + dy, text=txt, font=font, fill="black",
                               state="hidden")
            outs.append(o)
        outline_items.append(outs)
        metin.append(cv.create_text(0, cy, text=txt, font=font, fill="#000000"))

    sahne(0)


def sahne(k):
    # Kalpler yukarı süzülür
    for h in hearts:
        h["y"] += h["v"]
        if h["y"] > 340:
            h["y"] = -340
            h["x"] = r.randint(-300, 300)
        x = h["x"] + 18 * m.sin(k * 0.03 + h["ph"])
        cv.coords(h["id"], *pts_heart(x, h["y"], h["s"]))

    # Atan kalp
    nabiz = 1 + 0.36 * max(0, m.sin(k * 0.25)) ** 2
    cv.coords(beat, *pts_heart(0, -105, 13 * nabiz))

    # Yazılar yavaşça belirir (ilk ~80 karede), sonra "nefes alır"
    a1 = min(1, k / 40)
    a2 = max(0, min(1, (k - 40) / 40))
    nefes = 0.88 + 0.12 * m.sin(k * 0.1) if k > 80 else 1
    if k == 1:
        for o in outline_items[0]:
            cv.itemconfigure(o, state="normal")
    if k == 41:
        for o in outline_items[1]:
            cv.itemconfigure(o, state="normal")
    if k <= 80 or k % 2 == 0:
        c1 = hexc(*(v * a1 * nefes for v in PEMBE1))
        c2 = hexc(*(v * a2 for v in PEMBE2))
        cv.itemconfigure(metin[0], fill=c1)
        cv.itemconfigure(metin[1], fill=c2)

    # Yazı hep en üstte kalsın
    for outs in outline_items:
        for o in outs:
            cv.tag_raise(o)
    for tx in metin:
        cv.tag_raise(tx)

    s.ontimer(lambda: sahne(k + 1), 30)


katman1()
t.done()
