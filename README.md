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
