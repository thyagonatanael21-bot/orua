-- ═══════════════════════════════════════════════════════════════
-- ✦ Hub — Base com Engrenagem
-- ═══════════════════════════════════════════════════════════════
print("[HUB] Boot...")

local Players         = game:GetService("Players")
local TweenService    = game:GetService("TweenService")
local UIS             = game:GetService("UserInputService")
local CoreGui         = game:GetService("CoreGui")
local LocalPlayer     = Players.LocalPlayer

-- ═══════════════ CONFIG ═══════════════
local CONFIG = {
    HubName = "Hub",
    BUTTON_IMAGE_ID = "",
}

-- ═══════════════ CLEANUP ═══════════════
pcall(function()
    local old = CoreGui:FindFirstChild("CleanHub")
    if old then old:Destroy() end
    local pg = LocalPlayer:FindFirstChild("PlayerGui")
    if pg then local x = pg:FindFirstChild("CleanHub"); if x then x:Destroy() end end
end)

-- ═══════════════ TEMA ═══════════════
local Theme = {
    Bg=Color3.fromRGB(13,14,22), Bg2=Color3.fromRGB(20,22,34),
    Panel=Color3.fromRGB(25,28,42), PanelLight=Color3.fromRGB(36,40,58),
    PanelHover=Color3.fromRGB(52,57,80), Sidebar=Color3.fromRGB(16,18,28),
    Accent=Color3.fromRGB(140,170,255), Accent2=Color3.fromRGB(200,165,255),
    AccentD=Color3.fromRGB(110,135,215), Glow=Color3.fromRGB(210,225,255),
    Text=Color3.fromRGB(245,240,252), TextDim=Color3.fromRGB(165,170,190),
    TextSoft=Color3.fromRGB(100,105,130), Stroke=Color3.fromRGB(44,48,66),
    StrokeL=Color3.fromRGB(64,70,92), Danger=Color3.fromRGB(255,115,135),
    Success=Color3.fromRGB(120,225,175),
}

-- ═══════════════ HELPERS ═══════════════
local function new(class, props)
    local o = Instance.new(class)
    for k, v in pairs(props or {}) do o[k] = v end
    return o
end
local function corner(p, r)
    local c = Instance.new("UICorner"); c.CornerRadius = UDim.new(0, r or 8); c.Parent = p; return c
end
local function stroke(p, c, t)
    local s = Instance.new("UIStroke")
    s.Color = c or Theme.Stroke; s.Thickness = t or 1
    s.ApplyStrokeMode = Enum.ApplyStrokeMode.Border; s.Parent = p; return s
end
local function gradient(p, c1, c2, rot)
    local g = Instance.new("UIGradient")
    g.Color = ColorSequence.new(c1, c2); g.Rotation = rot or 90; g.Parent = p; return g
end
local function tw(o, p, t, s, d)
    return TweenService:Create(o, TweenInfo.new(t or 0.3, s or Enum.EasingStyle.Quart, d or Enum.EasingDirection.Out), p)
end

-- ═══════════════ STATE ═══════════════
local State = {
    UIOpen = false,
    Minimized = false,
    ConfirmOpen = false,
    SettingsOpen = false,
    Destroyed = false,
    SavedPos = UDim2.new(0.5, -340, 0.55, -240),
}

-- ═══════════════ GUI ═══════════════
local Gui = new("ScreenGui", {
    Name="CleanHub", ResetOnSpawn=false,
    ZIndexBehavior=Enum.ZIndexBehavior.Sibling, IgnoreGuiInset=true,
})
pcall(function() Gui.Parent = CoreGui end)
if not Gui.Parent then pcall(function() Gui.Parent = LocalPlayer:WaitForChild("PlayerGui") end) end
print("[HUB] GUI OK")

-- ═══════════════ BOTÃO FLUTUANTE ═══════════════
local BTN_SIZE = 52
local FloatBtn = new("TextButton", {
    Size=UDim2.fromOffset(BTN_SIZE, BTN_SIZE),
    Position=UDim2.new(0, 20, 0.3, -BTN_SIZE/2),
    BackgroundColor3=Color3.fromRGB(8, 9, 12),
    Text="", AutoButtonColor=false, ClipsDescendants=true,
    ZIndex=200, Active=true, Parent=Gui,
})
corner(FloatBtn, BTN_SIZE/2)
local btnStroke = stroke(FloatBtn, Color3.fromRGB(45, 48, 58), 1.5)

if CONFIG.BUTTON_IMAGE_ID ~= "" then
    local img = new("ImageLabel", {
        Size=UDim2.fromScale(1,1), BackgroundTransparency=1,
        Image=CONFIG.BUTTON_IMAGE_ID, ScaleType=Enum.ScaleType.Crop,
        ZIndex=201, Parent=FloatBtn,
    })
    corner(img, BTN_SIZE/2)
end

local btnDragging, btnMoved = false, false
local btnDragStart, btnStartPos = nil, nil

FloatBtn.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1
    or input.UserInputType == Enum.UserInputType.Touch then
        btnDragging=true; btnMoved=false
        btnDragStart=input.Position; btnStartPos=FloatBtn.Position
    end
end)
UIS.InputChanged:Connect(function(input)
    if not btnDragging then return end
    if input.UserInputType ~= Enum.UserInputType.MouseMovement
    and input.UserInputType ~= Enum.UserInputType.Touch then return end
    local d = input.Position - btnDragStart
    if math.abs(d.X) > 4 or math.abs(d.Y) > 4 then btnMoved = true end
    FloatBtn.Position = UDim2.new(
        btnStartPos.X.Scale, btnStartPos.X.Offset+d.X,
        btnStartPos.Y.Scale, btnStartPos.Y.Offset+d.Y)
end)
UIS.InputEnded:Connect(function(input)
    if input.UserInputType ~= Enum.UserInputType.MouseButton1
    and input.UserInputType ~= Enum.UserInputType.Touch then return end
    if not btnDragging then return end
    btnDragging = false
    if btnMoved then
        local abs = FloatBtn.AbsolutePosition
        local size = FloatBtn.AbsoluteSize
        local vp = workspace.CurrentCamera.ViewportSize
        local cx = abs.X + size.X/2; local cy = abs.Y + size.Y/2
        local snapX = (cx < vp.X/2) and 20 or (vp.X-size.X-20)
        local snapY = math.clamp(cy-size.Y/2, 20, vp.Y-size.Y-20)
        tw(FloatBtn, {Position=UDim2.new(0, snapX, 0, snapY)}, 0.28, Enum.EasingStyle.Back):Play()
    end
end)
FloatBtn.MouseButton1Click:Connect(function()
    if btnMoved then return end
    if State.Destroyed then return end
    if State.UIOpen then
        if _G.HUB_closeUI then _G.HUB_closeUI() end
    else
        if _G.HUB_openUI then _G.HUB_openUI() end
    end
end)
FloatBtn.MouseEnter:Connect(function()
    if btnDragging then return end
    tw(FloatBtn, {Size=UDim2.fromOffset(BTN_SIZE+4, BTN_SIZE+4), BackgroundColor3=Color3.fromRGB(14,16,22)}, 0.2):Play()
    tw(btnStroke, {Color=Theme.Accent}, 0.2):Play()
end)
FloatBtn.MouseLeave:Connect(function()
    if btnDragging then return end
    tw(FloatBtn, {Size=UDim2.fromOffset(BTN_SIZE, BTN_SIZE), BackgroundColor3=Color3.fromRGB(8,9,12)}, 0.2):Play()
    tw(btnStroke, {Color=Color3.fromRGB(45,48,58)}, 0.2):Play()
end)

-- ═══════════════ JANELA ═══════════════
local W, H = 660, 480
local MIN_H = 58
local SB_W = 175

local Main = new("Frame", {
    Size=UDim2.fromOffset(W, H),
    Position=State.SavedPos,
    AnchorPoint=Vector2.new(0,0),
    BackgroundColor3=Theme.Bg, BorderSizePixel=0,
    ClipsDescendants=true, Active=true, Visible=false,
    ZIndex=10, Parent=Gui,
})
corner(Main, 16)
gradient(Main, Theme.Bg, Theme.Bg2, 145)
stroke(Main, Theme.StrokeL, 1.5)

local Header = new("Frame", {
    Size=UDim2.new(1,0,0,MIN_H),
    BackgroundColor3=Theme.Panel, BorderSizePixel=0,
    Active=true, ZIndex=12, Parent=Main,
})
corner(Header, 16)
new("Frame", {
    Size=UDim2.new(1,0,0,20), Position=UDim2.new(0,0,1,-20),
    BackgroundColor3=Theme.Panel, BorderSizePixel=0,
    ZIndex=12, Parent=Header,
})
local topLine = new("Frame", {
    Size=UDim2.new(1,0,0,2), BackgroundColor3=Theme.Accent,
    BorderSizePixel=0, ZIndex=13, Parent=Header,
})
gradient(topLine, Theme.Accent, Theme.Accent2, 0)

new("TextLabel", {
    Size=UDim2.new(0,260,0,24), Position=UDim2.fromOffset(18,16),
    BackgroundTransparency=1, Text=CONFIG.HubName,
    TextSize=16, TextColor3=Theme.Text,
    Font=Enum.Font.GothamBold,
    TextXAlignment=Enum.TextXAlignment.Left,
    ZIndex=13, Parent=Header,
})

local function headerBtn(txt, xOff, hoverColor)
    local b = new("TextButton", {
        Size=UDim2.fromOffset(28,28),
        Position=UDim2.new(1,xOff,0.5,-14),
        BackgroundColor3=Theme.PanelLight,
        Text=txt, TextSize=13, TextColor3=Theme.Text,
        Font=Enum.Font.GothamBold,
        AutoButtonColor=false,
        ZIndex=13, Parent=Header,
    })
    corner(b, 7); stroke(b, Theme.Stroke, 1)
    b.MouseEnter:Connect(function() tw(b, {BackgroundColor3=hoverColor or Theme.Accent}, 0.15):Play() end)
    b.MouseLeave:Connect(function() tw(b, {BackgroundColor3=Theme.PanelLight}, 0.15):Play() end)
    return b
end
local CloseBtn = headerBtn("✕", -42, Theme.Danger)
local MinBtn   = headerBtn("−", -76, Theme.Accent)
local GearBtn  = headerBtn("⚙", -110, Theme.Accent)

-- SIDEBAR
local Sidebar = new("Frame", {
    Size=UDim2.new(0,SB_W,1,-MIN_H),
    Position=UDim2.fromOffset(0,MIN_H),
    BackgroundColor3=Theme.Sidebar, BorderSizePixel=0,
    ZIndex=11, Parent=Main,
})
new("Frame", {
    Size=UDim2.new(0,2,1,0), Position=UDim2.new(1,-2,0,0),
    BackgroundColor3=Theme.Accent, BorderSizePixel=0,
    ZIndex=12, Parent=Sidebar,
})
new("TextLabel", {
    Size=UDim2.new(1,-24,0,14), Position=UDim2.fromOffset(16,12),
    BackgroundTransparency=1, Text="NAVEGAÇÃO",
    TextSize=9, TextColor3=Theme.TextSoft,
    Font=Enum.Font.GothamBold,
    TextXAlignment=Enum.TextXAlignment.Left,
    ZIndex=12, Parent=Sidebar,
})
local TabList = new("Frame", {
    Size=UDim2.new(1,-16,1,-48), Position=UDim2.fromOffset(8,38),
    BackgroundTransparency=1, ZIndex=12, Parent=Sidebar,
})
new("UIListLayout", {Padding=UDim.new(0,3), SortOrder=Enum.SortOrder.LayoutOrder, Parent=TabList})

-- CONTEÚDO
local Content = new("ScrollingFrame", {
    Size=UDim2.new(1,-SB_W-16,1,-MIN_H-16),
    Position=UDim2.fromOffset(SB_W+8,MIN_H+8),
    BackgroundTransparency=1, BorderSizePixel=0,
    ScrollBarThickness=4, ScrollBarImageColor3=Theme.StrokeL,
    AutomaticCanvasSize=Enum.AutomaticSize.Y,
    CanvasSize=UDim2.new(0,0,0,0),
    ZIndex=11, Parent=Main,
})
new("UIListLayout", {Padding=UDim.new(0,10), SortOrder=Enum.SortOrder.LayoutOrder, Parent=Content})
new("UIPadding", {PaddingRight=UDim.new(0,6), Parent=Content})

-- ═══════════════ CONFIRMAÇÃO ═══════════════
local ConfirmBackdrop = new("Frame", {
    Size=UDim2.fromScale(1,1),
    BackgroundColor3=Color3.new(0,0,0), BackgroundTransparency=1,
    BorderSizePixel=0, Visible=false,
    ZIndex=500, Active=true, Parent=Gui,
})
local confirmBlocker = new("TextButton", {
    Size=UDim2.fromScale(1,1), BackgroundTransparency=1, Text="",
    Modal=true, ZIndex=500, Parent=ConfirmBackdrop,
})
local ConfirmCard = new("Frame", {
    Size=UDim2.fromOffset(320,165),
    Position=UDim2.new(0.5,-160,0.5,-82),
    AnchorPoint=Vector2.new(0,0),
    BackgroundColor3=Theme.Bg, BorderSizePixel=0,
    ZIndex=501, Parent=ConfirmBackdrop,
})
corner(ConfirmCard, 14)
gradient(ConfirmCard, Theme.Bg, Theme.Bg2, 145)
stroke(ConfirmCard, Theme.StrokeL, 1.5)

new("Frame", {Size=UDim2.new(1,0,0,2), BackgroundColor3=Theme.Danger, BorderSizePixel=0, ZIndex=502, Parent=ConfirmCard})
new("TextLabel", {
    Size=UDim2.new(1,-40,0,24), Position=UDim2.fromOffset(20,24),
    BackgroundTransparency=1, Text="Fechar o hub?",
    TextSize=17, TextColor3=Theme.Text,
    Font=Enum.Font.GothamBold,
    TextXAlignment=Enum.TextXAlignment.Left,
    ZIndex=502, Parent=ConfirmCard,
})
new("TextLabel", {
    Size=UDim2.new(1,-40,0,36), Position=UDim2.fromOffset(20,54),
    BackgroundTransparency=1, Text="Tem certeza? O hub será encerrado.",
    TextSize=12, TextColor3=Theme.TextDim,
    Font=Enum.Font.GothamMedium,
    TextWrapped=true, TextXAlignment=Enum.TextXAlignment.Left,
    TextYAlignment=Enum.TextYAlignment.Top,
    ZIndex=502, Parent=ConfirmCard,
})

local noBtn = new("TextButton", {
    Size=UDim2.new(0.5,-24,0,36), Position=UDim2.fromOffset(16,110),
    BackgroundColor3=Theme.PanelLight, Text="Não",
    TextSize=13, TextColor3=Theme.Text,
    Font=Enum.Font.GothamBold,
    AutoButtonColor=false, ZIndex=502, Parent=ConfirmCard,
})
corner(noBtn, 8); stroke(noBtn, Theme.StrokeL, 1)
noBtn.MouseEnter:Connect(function() tw(noBtn, {BackgroundColor3=Theme.PanelHover}, 0.15):Play() end)
noBtn.MouseLeave:Connect(function() tw(noBtn, {BackgroundColor3=Theme.PanelLight}, 0.15):Play() end)

local yesBtn = new("TextButton", {
    Size=UDim2.new(0.5,-24,0,36), Position=UDim2.new(0.5,8,0,110),
    BackgroundColor3=Theme.Danger, Text="Sim",
    TextSize=13, TextColor3=Color3.new(1,1,1),
    Font=Enum.Font.GothamBold,
    AutoButtonColor=false, ZIndex=502, Parent=ConfirmCard,
})
corner(yesBtn, 8); stroke(yesBtn, Theme.Danger, 1)
yesBtn.MouseEnter:Connect(function() tw(yesBtn, {BackgroundColor3=Theme.Danger:Lerp(Color3.new(1,1,1), 0.15)}, 0.15):Play() end)
yesBtn.MouseLeave:Connect(function() tw(yesBtn, {BackgroundColor3=Theme.Danger}, 0.15):Play() end)

-- ═══════════════ PAINEL DE CONFIGURAÇÕES (vazio) ═══════════════
local SettingsPanel = new("Frame", {
    Size=UDim2.new(0,280,1,-MIN_H),
    Position=UDim2.new(1,0,0,MIN_H),
    BackgroundColor3=Theme.Bg, BorderSizePixel=0,
    ClipsDescendants=true, Visible=false,
    ZIndex=100, Parent=Main,
})
gradient(SettingsPanel, Theme.Bg, Theme.Bg2, 145)

new("Frame", {
    Size=UDim2.new(0,2,1,0), Position=UDim2.new(0,0,0,0),
    BackgroundColor3=Theme.Accent, BorderSizePixel=0,
    ZIndex=100, Parent=SettingsPanel,
})
new("Frame", {
    Size=UDim2.new(1,0,0,2), Position=UDim2.new(0,0,0,0),
    BackgroundColor3=Theme.Accent, BorderSizePixel=0,
    ZIndex=101, Parent=SettingsPanel,
})

new("TextLabel", {
    Size=UDim2.new(1,-50,0,20), Position=UDim2.fromOffset(18,14),
    BackgroundTransparency=1, Text="Configurações",
    TextSize=14, TextColor3=Theme.Text,
    Font=Enum.Font.GothamBold,
    TextXAlignment=Enum.TextXAlignment.Left,
    ZIndex=101, Parent=SettingsPanel,
})

local closeSettingsBtn = new("TextButton", {
    Size=UDim2.fromOffset(26,26), Position=UDim2.new(1,-36,0,12),
    BackgroundColor3=Theme.PanelLight, Text="✕", TextSize=11,
    TextColor3=Theme.Text, Font=Enum.Font.GothamBold,
    AutoButtonColor=false, ZIndex=101, Parent=SettingsPanel,
})
corner(closeSettingsBtn, 7)
closeSettingsBtn.MouseEnter:Connect(function() tw(closeSettingsBtn, {BackgroundColor3=Theme.Danger}, 0.15):Play() end)
closeSettingsBtn.MouseLeave:Connect(function() tw(closeSettingsBtn, {BackgroundColor3=Theme.PanelLight}, 0.15):Play() end)

local setScroll = new("ScrollingFrame", {
    Size=UDim2.new(1,-24,1,-60), Position=UDim2.fromOffset(12,50),
    BackgroundTransparency=1, BorderSizePixel=0,
    ScrollBarThickness=3, ScrollBarImageColor3=Theme.StrokeL,
    AutomaticCanvasSize=Enum.AutomaticSize.Y,
    CanvasSize=UDim2.new(0,0,0,0),
    ZIndex=101, Parent=SettingsPanel,
})
new("UIListLayout", {Padding=UDim.new(0,10), SortOrder=Enum.SortOrder.LayoutOrder, Parent=setScroll})

-- ═══════════════ FUNÇÕES ═══════════════
local function openConfirm()
    if State.ConfirmOpen then return end
    State.ConfirmOpen = true
    ConfirmBackdrop.Visible = true
    ConfirmBackdrop.BackgroundTransparency = 1
    ConfirmCard.Size = UDim2.fromOffset(0,0)
    tw(ConfirmBackdrop, {BackgroundTransparency=0.55}, 0.2):Play()
    tw(ConfirmCard, {Size=UDim2.fromOffset(320,165)}, 0.3, Enum.EasingStyle.Back):Play()
end
local function closeConfirm()
    if not State.ConfirmOpen then return end
    State.ConfirmOpen = false
    tw(ConfirmBackdrop, {BackgroundTransparency=1}, 0.2):Play()
    tw(ConfirmCard, {Size=UDim2.fromOffset(0,0)}, 0.2, Enum.EasingStyle.Back, Enum.EasingDirection.In):Play()
    task.delay(0.22, function() if not State.ConfirmOpen then ConfirmBackdrop.Visible = false end end)
end
local function openSettings()
    if State.SettingsOpen or State.Minimized then return end
    State.SettingsOpen = true
    SettingsPanel.Visible = true
    SettingsPanel.Position = UDim2.new(1, 280, 0, MIN_H)
    tw(SettingsPanel, {Position=UDim2.new(1,-280,0,MIN_H)}, 0.3, Enum.EasingStyle.Quart):Play()
    tw(GearBtn, {BackgroundColor3=Theme.Accent}, 0.2):Play()
end
local function closeSettings()
    if not State.SettingsOpen then return end
    State.SettingsOpen = false
    tw(SettingsPanel, {Position=UDim2.new(1,280,0,MIN_H)}, 0.25, Enum.EasingStyle.Quart):Play()
    tw(GearBtn, {BackgroundColor3=Theme.PanelLight}, 0.2):Play()
    task.delay(0.3, function() if not State.SettingsOpen then SettingsPanel.Visible = false end end)
end
local function closeUI()
    if not State.UIOpen then return end
    State.UIOpen = false
    closeSettings()
    State.SavedPos = Main.Position
    local t = tw(Main, {Size=UDim2.fromOffset(0,0)}, 0.28, Enum.EasingStyle.Back, Enum.EasingDirection.In)
    t.Completed:Connect(function() if not State.UIOpen then Main.Visible = false end end)
    t:Play()
end
local function openUI()
    if State.UIOpen or State.Destroyed then return end
    State.UIOpen = true
    Main.Position = State.SavedPos
    Main.Size = UDim2.fromOffset(0,0)
    Main.Visible = true
    tw(Main, {Size=UDim2.fromOffset(W,H)}, 0.4, Enum.EasingStyle.Back):Play()
end
local function toggleMinimize()
    if not State.UIOpen or State.Destroyed then return end
    State.Minimized = not State.Minimized
    if State.Minimized then
        Sidebar.Visible = false
        Content.Visible = false
        SettingsPanel.Visible = false
        State.SettingsOpen = false
        tw(Main, {Size=UDim2.fromOffset(W, MIN_H)}, 0.3, Enum.EasingStyle.Back):Play()
        MinBtn.Text = "+"
    else
        Sidebar.Visible = true
        Content.Visible = true
        tw(Main, {Size=UDim2.fromOffset(W, H)}, 0.3, Enum.EasingStyle.Back):Play()
        MinBtn.Text = "−"
    end
end
local function killHub()
    if State.Destroyed then return end
    State.Destroyed = true
    State.UIOpen = false
    closeConfirm()
    closeSettings()
    tw(Main, {Size=UDim2.fromOffset(0,0), BackgroundTransparency=1}, 0.35, Enum.EasingStyle.Back, Enum.EasingDirection.In):Play()
    tw(FloatBtn, {Size=UDim2.fromOffset(0,0), BackgroundTransparency=1}, 0.35, Enum.EasingStyle.Back, Enum.EasingDirection.In):Play()
    task.delay(0.4, function() pcall(function() Gui:Destroy() end) end)
end

_G.HUB_openUI = openUI
_G.HUB_closeUI = closeUI
_G.HUB_killHub = killHub

CloseBtn.MouseButton1Click:Connect(openConfirm)
MinBtn.MouseButton1Click:Connect(toggleMinimize)
GearBtn.MouseButton1Click:Connect(function()
    if State.SettingsOpen then closeSettings() else openSettings() end
end)
closeSettingsBtn.MouseButton1Click:Connect(closeSettings)
noBtn.MouseButton1Click:Connect(closeConfirm)
yesBtn.MouseButton1Click:Connect(killHub)
confirmBlocker.MouseButton1Click:Connect(closeConfirm)

-- ═══════════════ DRAG ═══════════════
local dragging, dragStart, startPos = false, nil, nil
local function isOverButton(pos)
    for _, btn in ipairs({CloseBtn, MinBtn, GearBtn}) do
        if btn and btn.Visible and btn.AbsoluteSize.X > 5 then
            local p = btn.AbsolutePosition
            local s = btn.AbsoluteSize
            if pos.X >= p.X-2 and pos.X <= p.X+s.X+2
               and pos.Y >= p.Y-2 and pos.Y <= p.Y+s.Y+2 then return true end
        end
    end
    return false
end
UIS.InputBegan:Connect(function(input, processed)
    if processed then return end
    if State.Destroyed or not State.UIOpen then return end
    if input.UserInputType ~= Enum.UserInputType.MouseButton1
       and input.UserInputType ~= Enum.UserInputType.Touch then return end
    local mp = UIS:GetMouseLocation()
    local hp = Header.AbsolutePosition
    local hs = Header.AbsoluteSize
    if mp.X < hp.X or mp.X > hp.X+hs.X
    or mp.Y < hp.Y or mp.Y > hp.Y+hs.Y then return end
    if isOverButton(mp) then return end
    dragging = true; dragStart = mp; startPos = Main.Position
end)
UIS.InputChanged:Connect(function(input)
    if not dragging or State.Destroyed then return end
    if input.UserInputType ~= Enum.UserInputType.MouseMovement
       and input.UserInputType ~= Enum.UserInputType.Touch then return end
    local mp = UIS:GetMouseLocation()
    local d = mp - dragStart
    local newX = startPos.X.Offset + d.X
    local newY = startPos.Y.Offset + d.Y
    local vp = workspace.CurrentCamera.ViewportSize
    newX = math.clamp(newX, -W+120, vp.X-120)
    newY = math.clamp(newY, -30, vp.Y-MIN_H-10)
    Main.Position = UDim2.new(startPos.X.Scale, newX, startPos.Y.Scale, newY)
end)
UIS.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1
       or input.UserInputType == Enum.UserInputType.Touch then dragging = false end
end)
UIS.InputBegan:Connect(function(input, processed)
    if processed or State.Destroyed then return end
    if input.KeyCode == Enum.KeyCode.Escape then
        if State.ConfirmOpen then closeConfirm()
        elseif State.SettingsOpen then closeSettings()
        elseif State.UIOpen then closeUI() end
    end
end)

-- Abre automaticamente
task.delay(0.3, function() openUI() end)

print("[HUB] ✅ PRONTO — engrenagem vazia adicionada")
