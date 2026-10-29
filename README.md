-- ═══════════════════════════════════════════════════════════════════════════════
-- TEMPLATE HUB - Só Interface
-- Key: 123
-- ═══════════════════════════════════════════════════════════════════════════════
print("[HUB] Boot...")

local Players = game:GetService("Players")
local UIS = game:GetService("UserInputService")
local TweenService = game:GetService("TweenService")
local LocalPlayer = Players.LocalPlayer

-- ═══════════ CONFIG ═══════════
local CHAVE = "123"
local TITULO = "MEU HUB"
local SUB = "TEMPLATE"

-- ═══════════ TEMA ═══════════
local T = {
    bg   = Color3.fromRGB(10, 14, 26),
    gl   = Color3.fromRGB(18, 24, 40),
    g2   = Color3.fromRGB(28, 36, 54),
    g3   = Color3.fromRGB(42, 52, 74),
    st   = Color3.fromRGB(60, 76, 110),
    ac   = Color3.fromRGB(59, 130, 246),
    ac2  = Color3.fromRGB(37, 99, 235),
    ac3  = Color3.fromRGB(147, 197, 253),
    tx   = Color3.fromRGB(248, 250, 252),
    td   = Color3.fromRGB(148, 163, 184),
    tm   = Color3.fromRGB(100, 116, 139),
    ok   = Color3.fromRGB(16, 185, 129),
    bad  = Color3.fromRGB(239, 68, 68),
    gd   = Color3.fromRGB(251, 191, 36),
    wr   = Color3.fromRGB(245, 158, 11),
}

-- ═══════════ HELPERS ═══════════
local function corner(p, r)
    local c = Instance.new("UICorner")
    c.CornerRadius = UDim.new(0, r or 10)
    c.Parent = p
    return c
end

local function stroke(p, c, t, tr)
    local s = Instance.new("UIStroke")
    s.Color = c or T.st
    s.Thickness = t or 1
    s.Transparency = tr or 0
    s.Parent = p
    return s
end

local function grad(p, c1, c2, r)
    local g = Instance.new("UIGradient")
    g.Color = ColorSequence.new(c1, c2)
    g.Rotation = r or 0
    g.Parent = p
    return g
end

local function pad(p, a, b, c, d)
    local u = Instance.new("UIPadding")
    u.PaddingTop = UDim.new(0, a or 0)
    u.PaddingBottom = UDim.new(0, b or 0)
    u.PaddingLeft = UDim.new(0, c or 0)
    u.PaddingRight = UDim.new(0, d or 0)
    u.Parent = p
    return u
end

local function pressAnim(btn)
    local o = btn.Size
    btn.MouseButton1Down:Connect(function()
        TweenService:Create(btn, TweenInfo.new(0.08), {
            Size = UDim2.new(o.X.Scale*0.97, o.X.Offset*0.97, o.Y.Scale*0.94, o.Y.Offset*0.94)
        }):Play()
    end)
    btn.MouseButton1Up:Connect(function()
        TweenService:Create(btn, TweenInfo.new(0.18), {Size = o}):Play()
    end)
end

-- ═══════════ GUI BASE ═══════════
pcall(function()
    local old = game:GetService("CoreGui"):FindFirstChild("TemplateHub")
    if old then old:Destroy() end
end)

local gui = Instance.new("ScreenGui")
gui.Name = "TemplateHub"
gui.ResetOnSpawn = false
gui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
gui.DisplayOrder = 999

local ok = false
pcall(function()
    gui.Parent = game:GetService("CoreGui")
    ok = gui.Parent ~= nil
end)
if not ok then
    local pg = LocalPlayer:WaitForChild("PlayerGui", 5)
    if pg then gui.Parent = pg; ok = true end
end
if not ok then warn("[HUB] GUI falhou"); return end

-- ═══════════ NOTIFICAÇÕES ═══════════
local nHolder = Instance.new("Frame")
nHolder.Size = UDim2.new(0, 300, 1, -40)
nHolder.Position = UDim2.new(1, -320, 0, 20)
nHolder.BackgroundTransparency = 1
nHolder.ZIndex = 200
nHolder.Parent = gui
Instance.new("UIListLayout", nHolder).Padding = UDim.new(0, 6)

local function notif(msg, cor, dur)
    cor = cor or T.ac
    dur = dur or 3
    local n = Instance.new("Frame")
    n.Size = UDim2.new(1, 0, 0, 44)
    n.BackgroundColor3 = T.gl
    n.BackgroundTransparency = 0.05
    n.ZIndex = 201
    n.Parent = nHolder
    corner(n, 8)
    stroke(n, cor, 1, 0.3)
    local bar = Instance.new("Frame")
    bar.Size = UDim2.new(0, 3, 1, -8)
    bar.Position = UDim2.new(0, 4, 0, 4)
    bar.BackgroundColor3 = cor
    bar.BorderSizePixel = 0
    bar.ZIndex = 202
    bar.Parent = n
    corner(bar, 2)
    local txt = Instance.new("TextLabel")
    txt.Size = UDim2.new(1, -24, 1, 0)
    txt.Position = UDim2.new(0, 14, 0, 0)
    txt.BackgroundTransparency = 1
    txt.Text = msg
    txt.TextColor3 = T.tx
    txt.Font = Enum.Font.Gotham
    txt.TextSize = 12
    txt.TextXAlignment = Enum.TextXAlignment.Left
    txt.TextWrapped = true
    txt.ZIndex = 202
    txt.Parent = n
    n.Position = UDim2.new(1, 60, 0, 0)
    TweenService:Create(n, TweenInfo.new(0.3), {Position = UDim2.new(0,0,0,0)}):Play()
    task.delay(dur, function()
        TweenService:Create(n, TweenInfo.new(0.3), {Position = UDim2.new(1,60,0,0), BackgroundTransparency = 1}):Play()
        TweenService:Create(txt, TweenInfo.new(0.3), {TextTransparency = 1}):Play()
        task.wait(0.4)
        n:Destroy()
    end)
end

-- ═══════════ LOCK ═══════════
local lock = Instance.new("Frame")
lock.Size = UDim2.new(0, 420, 0, 460)
lock.Position = UDim2.new(0.5, -210, 0.5, -230)
lock.BackgroundColor3 = T.gl
lock.BackgroundTransparency = 0.02
lock.ZIndex = 51
lock.Parent = gui
corner(lock, 16)
stroke(lock, T.ac, 1.5, 0.2)

local lockTop = Instance.new("Frame")
lockTop.Size = UDim2.new(1, -24, 0, 3)
lockTop.Position = UDim2.new(0, 12, 0, 0)
lockTop.BackgroundColor3 = T.ac
lockTop.BorderSizePixel = 0
lockTop.ZIndex = 52
lockTop.Parent = lock
corner(lockTop, 2)
grad(lockTop, T.ac, T.ac3, 0)

local logo = Instance.new("TextLabel")
logo.Size = UDim2.new(1, 0, 0, 80)
logo.Position = UDim2.new(0, 0, 0, 24)
logo.BackgroundTransparency = 1
logo.Text = "*"
logo.TextColor3 = T.ac
logo.Font = Enum.Font.GothamBlack
logo.TextSize = 60
logo.ZIndex = 52
logo.Parent = lock

local titulo = Instance.new("TextLabel")
titulo.Size = UDim2.new(1, 0, 0, 36)
titulo.Position = UDim2.new(0, 0, 0, 110)
titulo.BackgroundTransparency = 1
titulo.Text = TITULO
titulo.TextColor3 = T.tx
titulo.Font = Enum.Font.GothamBlack
titulo.TextSize = 24
titulo.ZIndex = 52
titulo.Parent = lock
grad(titulo, T.ac3, T.ac, 0)

local subtit = Instance.new("TextLabel")
subtit.Size = UDim2.new(1, 0, 0, 20)
subtit.Position = UDim2.new(0, 0, 0, 148)
subtit.BackgroundTransparency = 1
subtit.Text = SUB
subtit.TextColor3 = T.td
subtit.Font = Enum.Font.Gotham
subtit.TextSize = 11
subtit.ZIndex = 52
subtit.Parent = lock

local card = Instance.new("Frame")
card.Size = UDim2.new(1, -64, 0, 180)
card.Position = UDim2.new(0, 32, 0, 190)
card.BackgroundColor3 = T.g2
card.BackgroundTransparency = 0.25
card.ZIndex = 52
card.Parent = lock
corner(card, 10)
stroke(card, T.st, 1.5, 0.4)

local cardLbl = Instance.new("TextLabel")
cardLbl.Size = UDim2.new(1, -24, 0, 16)
cardLbl.Position = UDim2.new(0, 12, 0, 12)
cardLbl.BackgroundTransparency = 1
cardLbl.Text = "KEY DE ACESSO"
cardLbl.TextColor3 = T.ac3
cardLbl.Font = Enum.Font.GothamBold
cardLbl.TextSize = 11
cardLbl.TextXAlignment = Enum.TextXAlignment.Left
cardLbl.ZIndex = 53
cardLbl.Parent = card

local input = Instance.new("TextBox")
input.Size = UDim2.new(1, -24, 0, 44)
input.Position = UDim2.new(0, 12, 0, 36)
input.BackgroundColor3 = T.bg
input.BackgroundTransparency = 0.3
input.PlaceholderText = "digite a key..."
input.PlaceholderColor3 = T.tm
input.Text = ""
input.TextColor3 = T.tx
input.Font = Enum.Font.Gotham
input.TextSize = 13
input.ClearTextOnFocus = false
input.TextXAlignment = Enum.TextXAlignment.Left
input.ZIndex = 53
input.Parent = card
corner(input, 8)
local inputStroke = stroke(input, T.st, 1, 0.3)
pad(input, 0, 0, 12, 0)

local btnUnlock = Instance.new("TextButton")
btnUnlock.Size = UDim2.new(1, -24, 0, 42)
btnUnlock.Position = UDim2.new(0, 12, 0, 88)
btnUnlock.BackgroundColor3 = T.ac
btnUnlock.Text = "DESBLOQUEAR"
btnUnlock.TextColor3 = Color3.new(1, 1, 1)
btnUnlock.Font = Enum.Font.GothamBold
btnUnlock.TextSize = 13
btnUnlock.ZIndex = 53
btnUnlock.Parent = card
corner(btnUnlock, 8)
grad(btnUnlock, T.ac, T.ac2, 25)
pressAnim(btnUnlock)

local keyInfo = Instance.new("TextLabel")
keyInfo.Size = UDim2.new(1, -24, 0, 20)
keyInfo.Position = UDim2.new(0, 12, 0, 140)
keyInfo.BackgroundTransparency = 1
keyInfo.Text = ""
keyInfo.TextColor3 = T.ac3
keyInfo.Font = Enum.Font.GothamBold
keyInfo.TextSize = 11
keyInfo.ZIndex = 53
keyInfo.Parent = card

-- ═══════════ MAIN ═══════════
local MW, MH = 740, 500
local SW = 190

local main = Instance.new("Frame")
main.Size = UDim2.new(0, MW, 0, MH)
main.Position = UDim2.new(0.5, -MW/2, 0.5, -MH/2)
main.BackgroundColor3 = T.gl
main.BackgroundTransparency = 0.02
main.Active = true
main.Visible = false
main.ZIndex = 10
main.Parent = gui
corner(main, 16)
stroke(main, T.ac, 1.5, 0.25)

local topBar = Instance.new("Frame")
topBar.Size = UDim2.new(1, -20, 0, 3)
topBar.Position = UDim2.new(0, 10, 0, 0)
topBar.BackgroundColor3 = T.ac3
topBar.BorderSizePixel = 0
topBar.ZIndex = 15
topBar.Parent = main
corner(topBar, 2)
grad(topBar, T.ac, T.ac3, 0)

-- Drag
local drag, ds, sp2
main.InputBegan:Connect(function(i)
    if i.UserInputType == Enum.UserInputType.MouseButton1 or i.UserInputType == Enum.UserInputType.Touch then
        drag = true; ds = i.Position; sp2 = main.Position
        i.Changed:Connect(function()
            if i.UserInputState == Enum.UserInputState.End then drag = false end
        end)
    end
end)
main.InputChanged:Connect(function(i)
    if drag and (i.UserInputType == Enum.UserInputType.MouseMovement or i.UserInputType == Enum.UserInputType.Touch) then
        local d = i.Position - ds
        main.Position = UDim2.new(sp2.X.Scale, sp2.X.Offset+d.X, sp2.Y.Scale, sp2.Y.Offset+d.Y)
    end
end)

-- ═══════════ SIDEBAR (VAZIA) ═══════════
local sidebar = Instance.new("Frame")
sidebar.Size = UDim2.new(0, SW, 1, -12)
sidebar.Position = UDim2.new(0, 6, 0, 6)
sidebar.BackgroundColor3 = T.g2
sidebar.BackgroundTransparency = 0.15
sidebar.ZIndex = 11
sidebar.Parent = main
corner(sidebar, 12)
stroke(sidebar, T.st, 1, 0.4)

local sideTitle = Instance.new("TextLabel")
sideTitle.Size = UDim2.new(1, -20, 0, 24)
sideTitle.Position = UDim2.new(0, 12, 0, 12)
sideTitle.BackgroundTransparency = 1
sideTitle.Text = TITULO
sideTitle.TextColor3 = T.tx
sideTitle.Font = Enum.Font.GothamBlack
sideTitle.TextSize = 14
sideTitle.TextXAlignment = Enum.TextXAlignment.Left
sideTitle.ZIndex = 13
sideTitle.Parent = sidebar

local sideSub = Instance.new("TextLabel")
sideSub.Size = UDim2.new(1, -20, 0, 14)
sideSub.Position = UDim2.new(0, 12, 0, 36)
sideSub.BackgroundTransparency = 1
sideSub.Text = SUB
sideSub.TextColor3 = T.td
sideSub.Font = Enum.Font.Gotham
sideSub.TextSize = 9
sideSub.TextXAlignment = Enum.TextXAlignment.Left
sideSub.ZIndex = 13
sideSub.Parent = sidebar

local sideDiv = Instance.new("Frame")
sideDiv.Size = UDim2.new(1, -16, 0, 1)
sideDiv.Position = UDim2.new(0, 8, 0, 56)
sideDiv.BackgroundColor3 = T.st
sideDiv.BorderSizePixel = 0
sideDiv.ZIndex = 12
sideDiv.Parent = sidebar

-- 👇 AQUI VÃO AS ABAS (vazio por enquanto, quem usar preenche)
local tabsHolder = Instance.new("Frame")
tabsHolder.Name = "tabsHolder"
tabsHolder.Size = UDim2.new(1, -12, 0, 0)
tabsHolder.Position = UDim2.new(0, 6, 0, 66)
tabsHolder.BackgroundTransparency = 1
tabsHolder.AutomaticSize = Enum.AutomaticSize.Y
tabsHolder.ZIndex = 12
tabsHolder.Parent = sidebar
Instance.new("UIListLayout", tabsHolder).Padding = UDim.new(0, 3)

-- ═══════════ RIGHT AREA ═══════════
local right = Instance.new("Frame")
right.Size = UDim2.new(1, -SW - 18, 1, -12)
right.Position = UDim2.new(0, SW + 12, 0, 6)
right.BackgroundTransparency = 1
right.ZIndex = 11
right.Parent = main

local topHeader = Instance.new("Frame")
topHeader.Size = UDim2.new(1, 0, 0, 44)
topHeader.BackgroundColor3 = T.g2
topHeader.BackgroundTransparency = 0.15
topHeader.ZIndex = 12
topHeader.Parent = right
corner(topHeader, 10)
stroke(topHeader, T.st, 1, 0.4)

local topTitle = Instance.new("TextLabel")
topTitle.Size = UDim2.new(0, 300, 1, 0)
topTitle.Position = UDim2.new(0, 16, 0, 0)
topTitle.BackgroundTransparency = 1
topTitle.Text = "PAINEL"
topTitle.TextColor3 = T.ac3
topTitle.Font = Enum.Font.GothamBold
topTitle.TextSize = 12
topTitle.TextXAlignment = Enum.TextXAlignment.Left
topTitle.ZIndex = 13
topTitle.Parent = topHeader

local btnMin = Instance.new("TextButton")
btnMin.Size = UDim2.new(0, 30, 0, 30)
btnMin.Position = UDim2.new(1, -70, 0, 7)
btnMin.BackgroundColor3 = T.g3
btnMin.BackgroundTransparency = 0.3
btnMin.Text = "-"
btnMin.TextColor3 = T.tx
btnMin.Font = Enum.Font.GothamBold
btnMin.TextSize = 18
btnMin.ZIndex = 13
btnMin.Parent = topHeader
corner(btnMin, 6)
pressAnim(btnMin)

local btnClose = Instance.new("TextButton")
btnClose.Size = UDim2.new(0, 30, 0, 30)
btnClose.Position = UDim2.new(1, -36, 0, 7)
btnClose.BackgroundColor3 = T.bad
btnClose.BackgroundTransparency = 0.3
btnClose.Text = "X"
btnClose.TextColor3 = T.tx
btnClose.Font = Enum.Font.GothamBold
btnClose.TextSize = 14
btnClose.ZIndex = 13
btnClose.Parent = topHeader
corner(btnClose, 6)
pressAnim(btnClose)
btnClose.MouseButton1Click:Connect(function()
    TweenService:Create(main, TweenInfo.new(0.3), {
        Size = UDim2.new(0, MW, 0, 0),
        Position = UDim2.new(0.5, -MW/2, 0.5, 0)
    }):Play()
    task.wait(0.35)
    gui.Enabled = false
end)

-- 👇 AQUI VAI O CONTEÚDO (vazio, quem usar preenche com ScrollingFrames)
local content = Instance.new("Frame")
content.Name = "content"
content.Size = UDim2.new(1, 0, 1, -54)
content.Position = UDim2.new(0, 0, 0, 54)
content.BackgroundTransparency = 1
content.ZIndex = 12
content.Parent = right

-- ═══════════ COMPONENTES PRONTOS ═══════════
-- Esses componentes ficam disponíveis pra quem for usar o template
local API = {}

function API.makeSection(parent, titulo, cor)
    local f = Instance.new("Frame")
    f.Size = UDim2.new(1, -20, 0, 20)
    f.BackgroundTransparency = 1
    f.Parent = parent
    f.LayoutOrder = #parent:GetChildren()
    local l = Instance.new("TextLabel")
    l.Size = UDim2.new(1, 0, 1, 0)
    l.BackgroundTransparency = 1
    l.Text = "> " .. titulo
    l.TextColor3 = cor or T.ac3
    l.Font = Enum.Font.GothamBold
    l.TextSize = 10
    l.TextXAlignment = Enum.TextXAlignment.Left
    l.Parent = f
    return f
end

function API.makeButton(parent, texto, cor, callback)
    local b = Instance.new("TextButton")
    b.Size = UDim2.new(1, -20, 0, 36)
    b.BackgroundColor3 = cor or T.ac
    b.BackgroundTransparency = 0.2
    b.Text = texto
    b.TextColor3 = T.tx
    b.Font = Enum.Font.GothamBold
    b.TextSize = 11
    b.Parent = parent
    b.LayoutOrder = #parent:GetChildren()
    corner(b, 10)
    stroke(b, cor or T.ac, 1, 0.5)
    pressAnim(b)
    b.MouseButton1Click:Connect(callback)
    return b
end

function API.makeToggle(parent, texto, inicial, callback, cor)
    cor = cor or T.ok
    local F = Instance.new("Frame")
    F.Size = UDim2.new(1, -20, 0, 38)
    F.BackgroundColor3 = T.g2
    F.BackgroundTransparency = 0.2
    F.Parent = parent
    F.LayoutOrder = #parent:GetChildren()
    corner(F, 10)
    stroke(F, T.st, 1, 0.5)
    local L = Instance.new("TextLabel")
    L.Size = UDim2.new(1, -60, 1, 0)
    L.Position = UDim2.new(0, 14, 0, 0)
    L.BackgroundTransparency = 1
    L.Text = texto
    L.TextColor3 = T.tx
    L.Font = Enum.Font.GothamBold
    L.TextSize = 11
    L.TextXAlignment = Enum.TextXAlignment.Left
    L.Parent = F
    local knob = Instance.new("Frame")
    knob.Size = UDim2.new(0, 24, 0, 24)
    knob.Position = inicial and UDim2.new(1, -32, 0.5, -12) or UDim2.new(0, 8, 0.5, -12)
    knob.BackgroundColor3 = inicial and cor or Color3.fromRGB(70, 85, 110)
    knob.Parent = F
    corner(knob, 12)
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(1, 0, 1, 0)
    btn.BackgroundTransparency = 1
    btn.Text = ""
    btn.Parent = F
    local state = inicial
    btn.MouseButton1Click:Connect(function()
        state = not state
        TweenService:Create(knob, TweenInfo.new(0.25, Enum.EasingStyle.Back), {
            Position = state and UDim2.new(1, -32, 0.5, -12) or UDim2.new(0, 8, 0.5, -12),
            BackgroundColor3 = state and cor or Color3.fromRGB(70, 85, 110)
        }):Play()
        callback(state)
    end)
    return F
end

function API.makeSlider(parent, texto, inicial, minV, maxV, callback, cor)
    cor = cor or T.ac
    local w = Instance.new("Frame")
    w.Size = UDim2.new(1, -20, 0, 50)
    w.BackgroundColor3 = T.g2
    w.BackgroundTransparency = 0.2
    w.Parent = parent
    w.LayoutOrder = #parent:GetChildren()
    corner(w, 10)
    stroke(w, T.st, 1, 0.5)
    local L = Instance.new("TextLabel")
    L.Size = UDim2.new(1, -24, 0, 18)
    L.Position = UDim2.new(0, 14, 0, 6)
    L.BackgroundTransparency = 1
    L.Text = texto .. ": " .. inicial
    L.TextColor3 = T.tx
    L.Font = Enum.Font.GothamBold
    L.TextSize = 11
    L.TextXAlignment = Enum.TextXAlignment.Left
    L.Parent = w
    local bar = Instance.new("Frame")
    bar.Size = UDim2.new(1, -28, 0, 8)
    bar.Position = UDim2.new(0, 14, 0, 28)
    bar.BackgroundColor3 = T.g3
    bar.Parent = w
    corner(bar, 4)
    local fill = Instance.new("Frame")
    fill.Size = UDim2.new((inicial - minV) / (maxV - minV), 0, 1, 0)
    fill.BackgroundColor3 = cor
    fill.Parent = bar
    corner(fill, 4)
    grad(fill, cor, T.ac3, 0)
    local drag = Instance.new("TextButton")
    drag.Size = UDim2.new(1, 0, 1, 0)
    drag.BackgroundTransparency = 1
    drag.Text = ""
    drag.Parent = bar
    local dragging = false
    drag.MouseButton1Down:Connect(function() dragging = true end)
    UIS.InputEnded:Connect(function(i)
        if i.UserInputType == Enum.UserInputType.MouseButton1 or i.UserInputType == Enum.UserInputType.Touch then
            dragging = false
        end
    end)
    drag.MouseMoved:Connect(function()
        if dragging then
            local mx = UIS:GetMouseLocation().X
            local pct = math.clamp((mx - bar.AbsolutePosition.X) / bar.AbsoluteSize.X, 0, 1)
            local val = math.floor(minV + pct * (maxV - minV))
            fill.Size = UDim2.new(pct, 0, 1, 0)
            L.Text = texto .. ": " .. val
            callback(val)
        end
    end)
    return w
end

function API.makeTabScroll()
    local sc = Instance.new("ScrollingFrame")
    sc.Size = UDim2.new(1, 0, 1, 0)
    sc.BackgroundTransparency = 1
    sc.BorderSizePixel = 0
    sc.ScrollBarThickness = 4
    sc.ScrollBarImageColor3 = T.ac
    sc.AutomaticCanvasSize = Enum.AutomaticSize.Y
    sc.CanvasSize = UDim2.new(0, 0, 0, 0)
    sc.Parent = content
    Instance.new("UIListLayout", sc).Padding = UDim.new(0, 5)
    return sc
end

-- ═══════════ MINIMIZE ═══════════
local min = false
local restore = Instance.new("TextButton")
restore.Size = UDim2.new(0, 120, 0, 32)
restore.Position = UDim2.new(0.5, -60, 0, 6)
restore.BackgroundColor3 = T.ac
restore.Text = "ABRIR"
restore.TextColor3 = Color3.new(1, 1, 1)
restore.Font = Enum.Font.GothamBold
restore.TextSize = 12
restore.Visible = false
restore.ZIndex = 30
restore.Parent = main
corner(restore, 8)
pressAnim(restore)

btnMin.MouseButton1Click:Connect(function()
    if not min then
        min = true
        TweenService:Create(main, TweenInfo.new(0.3), {Size = UDim2.new(0, MW, 0, 44)}):Play()
        sidebar.Visible = false
        right.Visible = false
        restore.Visible = true
    end
end)
restore.MouseButton1Click:Connect(function()
    if min then
        min = false
        restore.Visible = false
        TweenService:Create(main, TweenInfo.new(0.3), {Size = UDim2.new(0, MW, 0, MH)}):Play()
        sidebar.Visible = true
        right.Visible = true
    end
end)

-- ═══════════ UNLOCK ═══════════
local function shake(f)
    local o = f.Position
    local s = {
        {0.04, UDim2.new(o.X.Scale, o.X.Offset-10, o.Y.Scale, o.Y.Offset)},
        {0.08, UDim2.new(o.X.Scale, o.X.Offset+10, o.Y.Scale, o.Y.Offset)},
        {0.12, UDim2.new(o.X.Scale, o.X.Offset-6, o.Y.Scale, o.Y.Offset)},
        {0.16, o},
    }
    for _, t in ipairs(s) do
        task.delay(t[1], function()
            TweenService:Create(f, TweenInfo.new(0.04), {Position = t[2]}):Play()
        end)
    end
end

local function unlock()
    for _, d in ipairs(lock:GetDescendants()) do
        if d:IsA("GuiObject") then
            pcall(function() TweenService:Create(d, TweenInfo.new(0.25), {BackgroundTransparency = 1}):Play() end)
        end
        if d:IsA("TextLabel") or d:IsA("TextButton") or d:IsA("TextBox") then
            pcall(function() TweenService:Create(d, TweenInfo.new(0.25), {TextTransparency = 1}):Play() end)
        end
        if d:IsA("UIStroke") then
            pcall(function() TweenService:Create(d, TweenInfo.new(0.25), {Transparency = 1}):Play() end)
        end
    end
    task.wait(0.3)
    lock.Visible = false
    main.Visible = true
    main.Size = UDim2.new(0, MW, 0, 240)
    main.Position = UDim2.new(0.5, -MW/2, 0.5, -120)
    main.BackgroundTransparency = 1
    TweenService:Create(main, TweenInfo.new(0.4), {
        BackgroundTransparency = 0.02,
        Size = UDim2.new(0, MW, 0, MH),
        Position = UDim2.new(0.5, -MW/2, 0.5, -MH/2),
    }):Play()
    notif("Bem-vindo!", T.ok, 4)
end

local function tentar()
    if input.Text == CHAVE then
        keyInfo.Text = "OK"
        keyInfo.TextColor3 = T.ok
        task.wait(0.3)
        unlock()
    else
        keyInfo.Text = "Key invalida"
        keyInfo.TextColor3 = T.bad
        shake(lock)
        TweenService:Create(inputStroke, TweenInfo.new(0.15), {Color = T.bad}):Play()
        task.delay(0.6, function()
            TweenService:Create(inputStroke, TweenInfo.new(0.15), {Color = T.st}):Play()
        end)
    end
end

btnUnlock.MouseButton1Click:Connect(tentar)
input.FocusLost:Connect(function(e) if e then tentar() end end)

-- ═══════════ EXPOE API GLOBAL ═══════════
-- Quem usar o template pode chamar: _G.Hub.makeButton(...)
_G.Hub = {
    -- Referências
    gui = gui,
    main = main,
    sidebar = sidebar,
    content = content,
    tabsHolder = tabsHolder,
    tema = T,
    -- Componentes
    makeSection = API.makeSection,
    makeButton = API.makeButton,
    makeToggle = API.makeToggle,
    makeSlider = API.makeSlider,
    makeTabScroll = API.makeTabScroll,
    -- Notificação
    notif = notif,
}

print("[HUB] Pronto! Key: " .. CHAVE)
print("[HUB] API disponivel em: _G.Hub")
notif("Digite a key para continuar", T.ac, 4)

-- ═══════════════════════════════════════════════════════════════════════════════
-- 📖 COMO ADICIONAR SUAS ABAS (exemplo — descomente e adapte)
-- ═══════════════════════════════════════════════════════════════════════════════
--[[

EXEMPLO 1: Adicionar uma aba simples
---------------------------------------
local minhaAba = _G.Hub.makeTabScroll()
_G.Hub.makeSection(minhaAba, "MINHA SECAO", _G.Hub.tema.ac)
_G.Hub.makeButton(minhaAba, "Meu Botao", _G.Hub.tema.ac, function()
    _G.Hub.notif("Clicou!", _G.Hub.tema.ok)
end)

-- Adicionar botão na sidebar que muda pra essa aba
local btnAba = Instance.new("TextButton")
btnAba.Size = UDim2.new(1, 0, 0, 34)
btnAba.BackgroundColor3 = _G.Hub.tema.g3
btnAba.Text = "  Minha Aba"
btnAba.TextColor3 = _G.Hub.tema.td
btnAba.Font = Enum.Font.GothamBold
btnAba.TextSize = 11
btnAba.TextXAlignment = Enum.TextXAlignment.Left
btnAba.Parent = _G.Hub.tabsHolder
btnAba.MouseButton1Click:Connect(function()
    minhaAba.Visible = true
end)


EXEMPLO 2: Adicionar toggle com função
---------------------------------------
local minhaAba = _G.Hub.makeTabScroll()
_G.Hub.makeToggle(minhaAba, "Speed Hack", false, function(ligado)
    local char = game.Players.LocalPlayer.Character
    local hum = char and char:FindFirstChildOfClass("Humanoid")
    if hum then
        hum.WalkSpeed = ligado and 100 or 16
    end
end)


EXEMPLO 3: Adicionar slider
----------------------------
_G.Hub.makeSlider(minhaAba, "Velocidade", 50, 16, 200, function(valor)
    local char = game.Players.LocalPlayer.Character
    local hum = char and char:FindFirstChildOfClass("Humanoid")
    if hum then hum.WalkSpeed = valor end
end)


EXEMPLO 4: Botão que faz TP
----------------------------
_G.Hub.makeButton(minhaAba, "TP Spawn", _G.Hub.tema.ok, function()
    local char = game.Players.LocalPlayer.Character
    local hrp = char and char:FindFirstChild("HumanoidRootPart")
    local spawn = workspace:FindFirstChildOfClass("SpawnLocation")
    if hrp and spawn then
        hrp.CFrame = spawn.CFrame + Vector3.new(0, 5, 0)
    end
end)

--]]
