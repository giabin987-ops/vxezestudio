local Fluent = loadstring(game:HttpGet("https://github.com/dawid-scripts/Fluent/releases/latest/download/main.lua"))()

local Window = Fluent:CreateWindow({
    Title = "vxeze hub",
    SubTitle = "by senpai",
    TabWidth = 160,
    Size = UDim2.fromOffset(580, 460),
    Acrylic = true,
    Theme = "Darker",
    MinimizeKey = Enum.KeyCode.LeftControl
})

local Tabs = {
    Main = Window:AddTab({ Title = "Main", Icon = "home" }),
    Settings = Window:AddTab({ Title = "Settings", Icon = "settings" })
}

local States = {
    Magnet = false,
    Reach = false
}

-- Dùng để track loop hiện tại, tránh nhiều loop chạy song song khi toggle nhanh
local LoopIDs = {
    Magnet = 0,
    Reach = 0
}

-------------------------------------------------------------------
-- 1. HÚT VẬT PHẨM (MAGNET ITEMS)
-------------------------------------------------------------------
Tabs.Main:AddToggle("MagnetToggle", {
    Title = "Hút vật phẩm (Magnet)",
    Description = "Hút đồ vật xung quanh ra sau lưng bạn",
    Default = false,
    Callback = function(Value)
        States.Magnet = Value
        LoopIDs.Magnet = LoopIDs.Magnet + 1
        local myID = LoopIDs.Magnet

        task.spawn(function()
            while States.Magnet and LoopIDs.Magnet == myID do
                local Player = game.Players.LocalPlayer
                local Char = Player.Character
                local Root = Char and Char:FindFirstChild("HumanoidRootPart")

                if Root then
                    -- Dùng GetDescendants() để quét toàn bộ Workspace, bắt cả item nằm trong Model/Folder
                    for _, item in pairs(game.Workspace:GetDescendants()) do
                        if item:IsA("BasePart")
                            and not item:IsDescendantOf(Char)
                            and item.Parent ~= game.Workspace -- bỏ qua terrain parts cấp cao nhất
                        then
                            -- Bỏ qua các part thuộc về character của người chơi khác
                            local ownerChar = item.Parent
                            local isOtherPlayer = false
                            for _, p in pairs(game.Players:GetPlayers()) do
                                if p ~= Player and p.Character and item:IsDescendantOf(p.Character) then
                                    isOtherPlayer = true
                                    break
                                end
                            end
                            if isOtherPlayer then continue end

                            local distance = (Root.Position - item.Position).Magnitude

                            if distance < 20 then
                                -- Kiểm tra part có thể thao tác được không (tránh lỗi khi part bị khóa server)
                                pcall(function()
                                    item.CanCollide = false
                                    item.Anchored = false
                                    -- Đặt vị trí sau lưng, offset nhỏ để tránh chồng nhau
                                    item.CFrame = Root.CFrame * CFrame.new(
                                        math.random(-2, 2),  -- lệch ngang để không chồng đống
                                        0,
                                        3
                                    )
                                    item.AssemblyLinearVelocity = Vector3.zero
                                    item.AssemblyAngularVelocity = Vector3.zero
                                end)
                            end
                        end
                    end
                end

                task.wait(0.05) -- Giảm từ mỗi frame xuống 0.05s để tránh lag
            end
        end)
    end
})

-------------------------------------------------------------------
-- 2. ĐÁNH TỪ XA (KILL AURA)
-------------------------------------------------------------------
Tabs.Main:AddToggle("ReachToggle", {
    Title = "Đánh từ xa (Kill Aura)",
    Description = "Zombie trong vòng bán kính sẽ bị tấn công",
    Default = false,
    Callback = function(Value)
        States.Reach = Value
        LoopIDs.Reach = LoopIDs.Reach + 1
        local myID = LoopIDs.Reach

        task.spawn(function()
            while States.Reach and LoopIDs.Reach == myID do
                local Player = game.Players.LocalPlayer
                local Char = Player.Character
                local Root = Char and Char:FindFirstChild("HumanoidRootPart")
                local Tool = Char and Char:FindFirstChildOfClass("Tool")

                if Root and Tool then
                    for _, enemy in pairs(game.Workspace:GetChildren()) do
                        local humanoid = enemy:FindFirstChild("Humanoid")
                        local enemyRoot = enemy:FindFirstChild("HumanoidRootPart")

                        if humanoid and enemyRoot and enemy ~= Char then
                            -- Lọc bỏ người chơi khác, chỉ tấn công NPC/Zombie
                            local isPlayer = game.Players:GetPlayerFromCharacter(enemy)
                            if isPlayer then continue end

                            -- Chỉ tấn công zombie còn sống
                            if humanoid.Health <= 0 then continue end

                            local dist = (Root.Position - enemyRoot.Position).Magnitude

                            if dist < 12 then -- Bán kính 12 studs, hợp lý hơn cho game này
                                -- pcall để tránh crash nếu Activate() gây lỗi
                                pcall(function()
                                    Tool:Activate()
                                end)
                            end
                        end
                    end
                end

                task.wait(0.1) -- Tốc độ đánh: 10 lần/giây
            end
        end)
    end
})

Window:SelectTab(1)

Fluent:Notify({
    Title = "vxeze hub",
    Content = "Script đã tải xong!",
    Duration = 5
})
