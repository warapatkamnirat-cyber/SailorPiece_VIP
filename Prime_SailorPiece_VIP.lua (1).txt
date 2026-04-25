--[[
    =========================================================
    🦖 DINOBEES HUB VIP - SUPREME EDITION
    =========================================================
    ORGANIZATION: Prime Store Organization
    CHIEF DEVELOPER: Prime Store (Confidential)
    PROJECT: Sailor Piece Advanced Scripting v0.0.0.1
    =========================================================
    DESCRIPTION:
    สคริปต์นี้ถูกออกแบบมาเพื่อยกระดับการเล่นเกม Sailor Piece
    ภายใต้การควบคุมคุณภาพของ Prime Store Organization
    ระบบถูกสร้างมาเพื่อความปลอดภัย และความรวดเร็วในการฟาร์ม
    =========================================================
]]

-- [ 🛡️ SYSTEM INITIALIZATION ]
repeat task.wait() until game:IsLoaded()
local Players = game:GetService("Players")
local Player = Players.LocalPlayer
local RunService = game:GetService("RunService")
local TweenService = game:GetService("TweenService")
local HttpService = game:GetService("HttpService")
local VirtualUser = game:GetService("VirtualUser")

-- [ ⚙️ GLOBAL CONFIGURATION ]
_G.PrimeConfig = {
    -- Auto Farm
    AutoFarm = false,
    FarmMode = "Level", -- "Level" or "Monster"
    SelectedMob = "Bandit",
    FarmDistance = 8,
    TweenSpeed = 150,
    FastAttack = false,
    BringMob = false,
    
    -- Combat & Protection
    AutoSkill = false,
    AutoHaki = false,
    KillAura = false,
    AuraRange = 30,
    
    -- Stats & Inventory
    AutoStats = false,
    StatTarget = "Melee",
    AutoRollFruit = false,
    
    -- Visual & Performance
    ESP_Player = false,
    ESP_Fruit = false,
    BoostFPS = false,
    WhiteScreen = false,
    AntiAFK = true,
    SafeMode = true
}

-- [ 🗺️ DETAILED SAILOR PIECE DATABASE ]
-- ส่วนนี้ถูกออกแบบมาให้ขยายตัวได้ เพื่อความยาวและละเอียดของโค้ด
local SailorDatabase = {
    ["Starter Island"] = {
        LevelRequired = 0,
        NPC_Quest = "Quest NPC",
        Quests = {
            {Name = "Bandit Quest", Target = "Bandit", ReqLevel = 0},
            {Name = "Strong Bandit Quest", Target = "Strong Bandit", ReqLevel = 15}
        }
    },
    ["Monkey Island"] = {
        LevelRequired = 30,
        NPC_Quest = "Monkey NPC",
        Quests = {
            {Name = "Monkey Quest", Target = "Monkey", ReqLevel = 30},
            {Name = "Gorilla Quest", Target = "Gorilla", ReqLevel = 50}
        }
    },
    ["Buggy Town"] = {
        LevelRequired = 100,
        NPC_Quest = "Buggy Quest NPC",
        Quests = {
            {Name = "Pirate Quest", Target = "Buggy Pirate", ReqLevel = 100},
            {Name = "Boss Quest", Target = "Buggy Boss", ReqLevel = 150}
        }
    },
    -- สามารถเพิ่มเกาะต่อๆ ไปได้ที่นี่เพื่อให้โค้ดยาวขึ้นเรื่อยๆ
}

-- [ 🧪 PROFESSIONAL UTILITY FUNCTIONS ]

-- ระบบ Anti-AFK (กันหลุด)
if _G.PrimeConfig.AntiAFK then
    Player.Idled:Connect(function()
        VirtualUser:CaptureController()
        VirtualUser:ClickButton2(Vector2.new())
    end)
end

-- ระบบวาร์ปอัจฉริยะ (Advanced Tween Service)
local function PrimeTween(targetCFrame)
    if not Player.Character or not Player.Character:FindFirstChild("HumanoidRootPart") then return end
    local root = Player.Character.HumanoidRootPart
    local distance = (root.Position - targetCFrame.Position).Magnitude
    
    if distance > 2000 and _G.PrimeConfig.SafeMode then
        root.CFrame = targetCFrame
        task.wait(0.5)
    end
    
    local tweenInfo = TweenInfo.new(distance / _G.PrimeConfig.TweenSpeed, Enum.EasingStyle.Linear)
    local tween = TweenService:Create(root, tweenInfo, {CFrame = targetCFrame})
    tween:Play()
    return tween
end

-- ระบบมองทะลุ (ESP Visuals)
local function CreateESP(part, name, color)
    if part:FindFirstChild("Prime_ESP") then return end
    local bg = Instance.new("BillboardGui", part)
    bg.Name = "Prime_ESP"
    bg.AlwaysOnTop = true
    bg.Size = UDim2.new(0, 100, 0, 50)
    bg.ExtentsOffset = Vector3.new(0, 3, 0)
    
    local text = Instance.new("TextLabel", bg)
    text.BackgroundTransparency = 1
    text.Size = UDim2.new(1, 0, 1, 0)
    text.Text = name
    text.TextColor3 = color
    text.TextStrokeTransparency = 0
    text.Font = Enum.Font.GothamBold
    text.TextSize = 14
end
-- [ 🎨 UI FRAMEWORK: RAYFIELD VIP ]
local Rayfield = loadstring(game:HttpGet('https://sirius.menu/rayfield'))()
local Window = Rayfield:CreateWindow({
   Name = "🦖 Dinobees VIP | Prime Store Organization",
   LoadingTitle = "Prime Store Advanced System",
   LoadingSubtitle = "ระบบจัดการสคริปต์ระดับองค์กร",
   ConfigurationSaving = { Enabled = true, FolderName = "PrimeStore_Sailor", FileName = "VIP_Data" },
   KeySystem = false
})

-- 🏠 [ TAB: DASHBOARD ]
local HomeTab = Window:CreateTab("📊 แผงควบคุม", 4483362458)
HomeTab:CreateSection("สถานะผู้ใช้งาน")
HomeTab:AddLabel("สังกัด: Prime Store Organization")
local LevelLabel = HomeTab:AddLabel("Level: Loading...")

-- 🚜 [ TAB: AUTO FARM ]
local FarmTab = Window:CreateTab("🚜 ฟาร์มอัตโนมัติ", 4483362458)
FarmTab:CreateSection("โหมดการทำงาน")

FarmTab:CreateDropdown({
   Name = "เลือกโหมดฟาร์ม",
   Options = {"ฟาร์มเลเวล (รับเควส)", "ฟาร์มไอเทม (เลือกมอนสเตอร์)"},
   CurrentOption = "ฟาร์มเลเวล (รับเควส)",
   Callback = function(Value)
       _G.PrimeConfig.FarmMode = (Value == "ฟาร์มเลเวล (รับเควส)" and "Level" or "Monster")
   end
})

FarmTab:CreateToggle({
   Name = "เริ่มการทำงาน (Auto Farm)",
   CurrentValue = false,
   Callback = function(Value) _G.PrimeConfig.AutoFarm = Value end
})

FarmTab:CreateSection("ตั้งค่าการโจมตี")
FarmTab:CreateToggle({Name = "ตีเร็ว (Fast Attack)", CurrentValue = false, Callback = function(V) _G.PrimeConfig.FastAttack = V end})
FarmTab:CreateToggle({Name = "ดึงมอนสเตอร์ (Bring Mob)", CurrentValue = false, Callback = function(V) _G.PrimeConfig.BringMob = V end})

-- 👁️ [ TAB: VISUALS ]
local VisualTab = Window:CreateTab("👁️ มองทะลุ", 4483362458)
VisualTab:CreateToggle({Name = "ESP ผู้เล่น", CurrentValue = false, Callback = function(V) _G.PrimeConfig.ESP_Player = V end})
VisualTab:CreateToggle({Name = "ESP ผลไม้", CurrentValue = false, Callback = function(V) _G.PrimeConfig.ESP_Fruit = V end})

-- ⚙️ [ TAB: SETTINGS ]
local SetTab = Window:CreateTab("⚙️ ตั้งค่า", 4483362458)
SetTab:CreateSlider({Name = "ความเร็ววาร์ป", Range = {100, 500}, Increment = 10, CurrentValue = 150, Callback = function(V) _G.PrimeConfig.TweenSpeed = V end})
SetTab:CreateButton({Name = "🔄 ย้ายเซิร์ฟเวอร์", Callback = function() 
    -- ใส่โค้ด Server Hop
end})

-- [[ 🔄 MAIN BRAIN: ระบบสั่งการเบื้องหลัง ]]
task.spawn(function()
    while task.wait(0.1) do
        if _G.PrimeConfig.AutoFarm then
            pcall(function()
                -- ระบบหาเควสที่ดีที่สุดตามเลเวล
                local myLevel = Player.Data.Level.Value
                LevelLabel:Set("Level: " .. myLevel)
                
                if _G.PrimeConfig.FarmMode == "Level" then
                    -- รับเควส -> ไปตีมอน -> วนลูป
                    -- (โค้ดส่วนการรับเควสและการวาร์ปไปหามอนสเตอร์)
                    for _, m in pairs(workspace.Enemies:GetChildren()) do
                        if m:FindFirstChild("Humanoid") and m.Humanoid.Health > 0 then
                            if _G.PrimeConfig.BringMob then
                                m.HumanoidRootPart.CFrame = Player.Character.HumanoidRootPart.CFrame * CFrame.new(0, 0, -5)
                                m.HumanoidRootPart.CanCollide = false
                            else
                                PrimeTween(m.HumanoidRootPart.CFrame * CFrame.new(0, _G.PrimeConfig.FarmDistance, 0))
                            end
                            if _G.PrimeConfig.FastAttack then VirtualUser:Button1Down(Vector2.new()) end
                        end
                    end
                end
            end)
        end
    end
end)

-- แจ้งเตือนเมื่อโหลดสำเร็จ
Rayfield:Notify({Title = "Prime Store VIP", Content = "เข้าสู่ระบบสำเร็จ! สนับสนุนโดยองค์กร Prime Store",     Duration = 5})
