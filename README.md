local Rayfield = loadstring(game:HttpGet('https://sirius.menu/rayfield'))()

local Window = Rayfield:CreateWindow({
	Name = "test scipt",
	Icon = 0, -- Icon in Topbar. Can use Lucide Icons (string) or Roblox Image (number). 0 to use no icon (default).
	LoadingTitle = "Rayfield Interface Suite",
	LoadingSubtitle = "by Mikey",
	Theme = "Default", -- Check https://docs.sirius.menu/rayfield/configuration/themes

	DisableRayfieldPrompts = false,
	DisableBuildWarnings = false, -- Prevents Rayfield from warning when the script has a version mismatch with the interface

	ConfigurationSaving = {
		Enabled = true,
		FolderName = nil, -- Create a custom folder for your hub/game
		FileName = "Mikey Hub"
	},

	Discord = {
		Enabled = false, -- Prompt the user to join your Discord server if their executor supports it
		Invite = "noinvitelink", -- The Discord invite code, do not include discord.gg/. E.g. discord.gg/ ABCD would be ABCD
		RememberJoins = true -- Set this to false to make them join the discord every time they load it up
	},

	KeySystem = true, -- Set this to true to use our key system
	KeySettings = {
		Title = "Enter The code",
		Subtitle = "Key System",
		Note = "Key:123", -- Use this to tell the user how to get a key
		FileName = "Key", -- It is recommended to use something unique as other scripts using Rayfield may overwrite your key file
		SaveKey = false, -- The user's key will be saved, but if you change the key, they will be unable to use your script
		GrabKeyFromSite = false, -- If this is true, set Key below to the RAW site you would like Rayfield to get the key from
		Key = {"123"} -- List of keys that will be accepted by the system, can be RAW file links (pastebin, github etc) or simple strings ("hello","key22")
	}
})

local Tab = Window:CreateTab("Player", 4483362458) -- Title, Image
local Section = Tab:CreateSection("Player Script")
local Slider = Tab:CreateSlider({
	Name = "Speed",
	Range = {0, 1000},
	Increment = 10,
	Suffix = "Bananas",
	CurrentValue = 10,
	Flag = "Slider1", -- A flag is the identifier for the configuration file, make sure every element has a different flag if you're using configuration saving to ensure no overlaps
	Callback = function(Value)
		game.Players.LocalPlayer.Character.Humanoid.WalkSpeed = Value
	end,
})

local Slider = Tab:CreateSlider({
	Name = "Jump",
	Range = {0, 1000},
	Increment = 10,
	Suffix = "Bananas",
	CurrentValue = 10,
	Flag = "Slider1", -- A flag is the identifier for the configuration file, make sure every element has a different flag if you're using configuration saving to ensure no overlaps
	Callback = function(Value)
		game.Players.LocalPlayer.Character.Humanoid.JumpPower = Value
	end,
})
local MainTab = Window:CreateTab("ESP", 4483362458)

-- ตัวแปรควบคุมสถานะ ESP
local espEnabled = false

-- ฟังก์ชันสร้าง Highlight ESP ให้ผู้เล่น
local function addESP(player)
	if player == game.Players.LocalPlayer then return end
	local char = player.Character
	if not char then return end
	if char:FindFirstChild("ESPHighlight") then return end

	local highlight = Instance.new("Highlight")
	highlight.Name = "ESPHighlight"
	highlight.FillColor = Color3.fromRGB(68, 255, 0)
	highlight.OutlineColor = Color3.fromRGB(251, 255, 0)
	highlight.FillTransparency = 0.3
	highlight.OutlineTransparency = 0
	highlight.Adornee = char
	highlight.Parent = char
end

-- ฟังก์ชันลบ Highlight
local function removeESP(player)
	local char = player.Character
	if char and char:FindFirstChild("ESPHighlight") then
		char.ESPHighlight:Destroy()
	end
end

-- เปิดหรือปิด ESP
local function toggleESP(state)
	espEnabled = state
	for _, player in pairs(game.Players:GetPlayers()) do
		if player ~= game.Players.LocalPlayer then
			if state then
				addESP(player)
			else
				removeESP(player)
			end
		end
	end
end

-- อัปเดต ESP หากมีผู้เล่นหรือร่างเกิดใหม่
game.Players.PlayerAdded:Connect(function(player)
	player.CharacterAdded:Connect(function()
		wait(1)
		if espEnabled then
			addESP(player)
		end
	end)
end)

-- ปุ่ม Toggle ESP บน GUI
MainTab:CreateToggle({
	Name = "Toggle ESP",
	CurrentValue = false,
	Flag = "ESP_Toggle",
	Callback = function(Value)
		toggleESP(Value)
	end,
})

local MainTab = Window:CreateTab("Teleport", 4483362458)

-- กล่อง TextBox สำหรับกรอกชื่อผู้เล่น
MainTab:CreateInput({
	Name = "Enter Player Name",
	PlaceholderText = "เช่น: Player123",
	RemoveTextAfterFocusLost = false,
	Callback = function(playerName)
		local targetPlayer = game.Players:FindFirstChild(playerName)
		local localPlayer = game.Players.LocalPlayer

		if not targetPlayer then
			Rayfield:Notify({
				Title = "ไม่พบผู้เล่น",
				Content = "ไม่มีผู้เล่นชื่อ '" .. playerName .. "' ในเซิร์ฟเวอร์",
				Duration = 4
			})
			return
		end

		if targetPlayer.Character and targetPlayer.Character:FindFirstChild("HumanoidRootPart") then
			local targetPos = targetPlayer.Character.HumanoidRootPart.Position + Vector3.new(0, 5, 0)
			if localPlayer.Character and localPlayer.Character:FindFirstChild("HumanoidRootPart") then
				localPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(targetPos)

				Rayfield:Notify({
					Title = "เทเลพอร์ตสำเร็จ",
					Content = "วาร์ปไปยัง " .. playerName .. " แล้ว",
					Duration = 3
				})
			end
		else
			Rayfield:Notify({
				Title = "ไม่สามารถวาร์ปได้",
				Content = playerName .. " ยังไม่โหลดตัวละครหรือออกจากเกม",
				Duration = 4
			})
		end
	end,
})
local MainTab = Window:CreateTab("Fly", 4483362458)

-- กำหนดค่าตัวแปร
local flying = false
local speed = 50  -- ความเร็วในการบิน

-- สร้าง Toggle สำหรับเปิด/ปิด Fly
MainTab:CreateToggle({
	Name = "เปิด/ปิด Fly",
	CurrentValue = false,
	Flag = "FlyToggle",
	Callback = function(Value)
		flying = Value
		if flying then
			StartFlying()
		else
			StopFlying()
		end
	end,
})

-- ฟังก์ชันเริ่มต้นการบิน
function StartFlying()
	local character = game.Players.LocalPlayer.Character
	if character and character:FindFirstChild("HumanoidRootPart") then
		local humanoid = character:WaitForChild("Humanoid")
		local bodyGyro = Instance.new("BodyGyro", character.HumanoidRootPart)
		local bodyVelocity = Instance.new("BodyVelocity", character.HumanoidRootPart)

		bodyGyro.MaxTorque = Vector3.new(400000, 400000, 400000)
		bodyGyro.D = 50
		bodyGyro.CFrame = character.HumanoidRootPart.CFrame

		bodyVelocity.MaxForce = Vector3.new(400000, 400000, 400000)
		bodyVelocity.Velocity = Vector3.new(0, 0, 0)

		-- บินขึ้นไป
		bodyVelocity.Velocity = Vector3.new(0, speed, 0)

		-- ควบคุมการบิน
		spawn(function()
			while flying do
				task.wait()
				local mouse = game.Players.LocalPlayer:GetMouse()
				local direction = (mouse.Hit.p - character.HumanoidRootPart.Position).unit
				bodyVelocity.Velocity = direction * speed + Vector3.new(0, 50, 0)
			end
		end)
	end
end

-- ฟังก์ชันหยุดการบิน
function StopFlying()
	local character = game.Players.LocalPlayer.Character
	if character and character:FindFirstChild("HumanoidRootPart") then
		local bodyVelocity = character.HumanoidRootPart:FindFirstChildOfClass("BodyVelocity")
		local bodyGyro = character.HumanoidRootPart:FindFirstChildOfClass("BodyGyro")

		if bodyVelocity then
			bodyVelocity:Destroy()
		end

		if bodyGyro then
			bodyGyro:Destroy()
		end
	end
end
